# **Обновление ядра**

Скопировал репозиторий:

```
git clone git@github.com:mashchensky/manual_kernel_update.git
```
Перешел в появившуюся директорию manual_kernel_update:
```
cd manual_kernel_update
```
Запустил виртуальную машину и залогинился:
```
vagrant up
vagrant ssh
```
Проверил текущую версию ядра:
```
[vagrant@kernel-update ~]$ uname -r
3.10.0-957.12.2.el7.x86_64
```
Подключил репозиторий, с необходимой версией ядра.
```
sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```
Поставил последнее ядро:
```
sudo yum --enablerepo elrepo-kernel install kernel-ml -y
```
Обновил конфигурацию загрузчика:
```
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```
Выбрал загрузку с новым ядром по-умолчанию:
```
sudo grub2-set-default 0
```
Перезагрузил виртуальную машину:
```
sudo reboot
```
После перезагрузки залогинился:
```
vagrant ssh
```
Проверил новую версию ядра:
```
[vagrant@kernel-update ~]$ uname -r
5.5.1-1.el7.elrepo.x86_64
```

---

# **Создание образа с обновленным ядром**

Для создания образа системы перешел в директорию `packer` и в ней выполнил команду:

```
packer build centos.json
```

В текущей директории появился файл `centos-7.7.1908-kernel-5-x86_64-Minimal.box`

Протестировал созданный образ, выполнив его импорт в `vagrant`:

```
vagrant box add --name centos-7-5 centos-7.7.1908-kernel-5-x86_64-Minimal.box
vagrant init centos-7-5
vagrant up
...
vagrant ssh
```
Проверил версию ядра:
```
[vagrant@kernel-update ~]$ uname -r
5.5.1-1.el7.elrepo.x86_64
```
Удалил тестовый образ из локального хранилища:
```
vagrant box remove centos-7-5
```
Залогинился в Vagrant Cloud:
```
vagrant cloud auth login
Vagrant Cloud username or email: mashchensky@ya.ru
Password (will be hidden): 
Token description (Defaults to "Vagrant login from DS-WS"):
You are now logged in.
```
Опубликовал полученный бокс:
```
vagrant cloud publish --release mashchensky/centos-7-5 1.0 virtualbox \
        centos-7.7.1908-kernel-5-x86_64-Minimal.box
```
