# otus_lesson1_custom_core
 CentOS 7 + Core 5.0.0-rc8



За пример была взята статья "https://www.dmosk.ru/miniinstruktions.php?mini=kernel-install-centos"

За основу VM был взят файл "Vagrantfile" из репозитория "manual_kernel_update".
В VM добавил памяти и ядра, для более быстрой сборки.

Далее как в руководстве по сборке:
sudo yum update -y
sudo shutdown -r now

Далее ставим wget
sudo yum install -y wget

Качаем архив с ядром
wget https://git.kernel.org/torvalds/t/linux-5.0-rc8.tar.gz

Распаковываем скачанный архив:
sudo tar -xvf linux-5.0-rc8.tar.gz -C /usr/src

Переходим в каталог, куда распаковали исходник ядра:
cd /usr/src/linux-5.0-rc8/

Устанавливаем инструменты для компиляции пакетов:
sudo yum groupinstall -y "Development Tools"
sudo yum install -y ncurses-devel openssl-devel bc

Так же необходим пакет "elfutils-libelf-devel", его в инструкции нет, ставим:
Ошибка:
error: Cannot generate ORC metadata for CONFIG_UNWINDER_ORC=y, please install libelf-dev, libelf-devel or elfutils-libelf-devel

sudo yum install -y elfutils-libelf-devel

Используем текущую конфигурацию для ядра:
sudo make oldconfig

На все вопросы, которые задает система можно отвечаю нажатием Enter, чтобы принимать значения по умолчанию.

Далее собираем ядром,и ставим ядро и модули:
sudo make all -j5 && sudo make modules_install install

Окончание сборки ядра у меня выглядело так:
  INSTALL sound/synth/emux/snd-emux-synth.ko
  INSTALL sound/synth/snd-util-mem.ko
  INSTALL sound/usb/6fire/snd-usb-6fire.ko
  INSTALL sound/usb/bcd2000/snd-bcd2000.ko
  INSTALL sound/usb/caiaq/snd-usb-caiaq.ko
  INSTALL sound/usb/hiface/snd-usb-hiface.ko
  INSTALL sound/usb/line6/snd-usb-line6.ko
  INSTALL sound/usb/line6/snd-usb-pod.ko
  INSTALL sound/usb/line6/snd-usb-podhd.ko
  INSTALL sound/usb/line6/snd-usb-toneport.ko
  INSTALL sound/usb/line6/snd-usb-variax.ko
  INSTALL sound/usb/misc/snd-ua101.ko
  INSTALL sound/usb/snd-usb-audio.ko
  INSTALL sound/usb/snd-usbmidi-lib.ko
  INSTALL sound/usb/usx2y/snd-usb-us122l.ko
  INSTALL sound/usb/usx2y/snd-usb-usx2y.ko
  INSTALL sound/x86/snd-hdmi-lpe-audio.ko
  INSTALL virt/lib/irqbypass.ko
  DEPMOD  5.0.0-rc8
sh ./arch/x86/boot/install.sh 5.0.0-rc8 arch/x86/boot/bzImage \
        System.map "/boot"
		
Далее перезагружаю систему и загружаюсь с новым ядром
[root@kernel-update linux-5.0-rc8]# shutdown -r now

PS C:\_git_\manual_kernel_update> vagrant ssh
Last login: Thu Feb  6 07:30:47 2020
[vagrant@kernel-update ~]$ uname -sr
Linux 5.0.0-rc8


После этого делаем новое ядро загружаемым по умолчанию, как в методичке:
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
sudo grub2-set-default 0

Мой вывод выполненных команд:
PS C:\_git_\manual_kernel_update> vagrant ssh
Last login: Thu Feb  6 07:24:11 2020
[vagrant@kernel-update ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.0.0-rc8
Found initrd image: /boot/initramfs-5.0.0-rc8.img
Found linux image: /boot/vmlinuz-3.10.0-1062.12.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1062.12.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-957.12.2.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-957.12.2.el7.x86_64.img
done
[vagrant@kernel-update ~]$ sudo grub2-set-default 0
[vagrant@kernel-update ~]$ sudo reboot
Connection to 127.0.0.1 closed by remote host.
Connection to 127.0.0.1 closed.
PS C:\_git_\manual_kernel_update> vagrant ssh
Last login: Thu Feb  6 07:26:28 2020










































