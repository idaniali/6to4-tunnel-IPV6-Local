# فعال سازی 6to4 tunnel IPV6 Local بدون نیاز به IPv6 

> Disclaimer: This project is only for personal learning and communication, please do not use it for illegal purposes, please do not use it in a production environment
> 
>این پروژه فقط برای یادگیری و ارتباط شخصی است، لطفا از آن برای مقاصد غیرقانونی استفاده نکنید.

اگر این پروژه برای شما مفید بود با ستاره دادن ⭐ و دونیت کردن از من حمایت کنید تا باعث انگیزه و ادامه دادن بنده در این مسیر شود .

- USDT (TRC20): `TG4fcAJoMcVjV8iaKFmC6AGxkhR15nMwcH`
- TRX : `TG4fcAJoMcVjV8iaKFmC6AGxkhR15nMwcH`
- BTC : `bc1qpme6r9fn6yc95wcu5gawwtunzgfg2w24022gld`



**Step 1:** 

ابتدا در سرور ایران باید این دستورات رو وارد کنیم تا تانل 6to4 در سرور ایران ، برقرار شود .
>راهنمایی : (در قسمت ip-Kharej ، آیپی ورژن 4 پابلیک سرور خارج رو قرار میدیم و در بخش ip-Iran ، آیپی ورژن 4 پابلیک ایران رو قرار میدیم)
```shell
ip tunnel add 6to4_To_KH mode sit remote IPv4-Kharej local IPv4-Iran
ip -6 addr add fc00::1/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up
```



**Step 2:** 

در این مرحله تانل 6to4 رو در سرور خارج برقرار میکنیم .
>راهنمایی : (در قسمت ip-Kharej ، آیپی ورژن 4 پابلیک سرور خارج رو قرار میدیم و در بخش ip-Iran ، آیپی ورژن 4 پابلیک ایران رو قرار میدیم)

```shell 
ip tunnel add 6to4_To_IR mode sit remote ip-Iran local ip-Kharej
ip -6 addr add fc00::2/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up
```
تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل 6to4 بین سرور خارج و ایران برقرار شده .
- آیپی v6 سرور ایران : fc00::1
- آیپی v6 سرور خارج : fc00::2

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن IPv6 استفاده میکنیم : 
```shell
ping6 fc00::2 
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping6 fc00::1
```


# فعال سازی تانل IPIPv6 یا GRE6

**Step 3:**

**در سرور ایران :**


برای راه اندازی تانل IPIPv6 یا GRE6 در سرور ایران یکی از دو دستور زیر رو اجرا کنید :
>توجه داشته باشید هر تانلی رو که انتخاب میکنید دقیقا در سرور خارج هم همان تانل رو باید استفاده کنید .

## IPIPv6 :
```shell
ip -6 tunnel add ipip6Tun_To_KH mode ipip6 remote fc00::2 local fc00::1
ip addr add 192.168.13.1/30 dev ipip6Tun_To_KH
ip link set ipip6Tun_To_KH mtu 1440
ip link set ipip6Tun_To_KH up
```
## GRE6:
```shell
ip -6 tunnel add GRE6Tun_To_KH mode ip6gre remote fc00::2 local fc00::1
ip addr add 192.168.13.1/30 dev GRE6Tun_To_KH
ip link set GRE6Tun_To_KH mtu 1436
ip link set GRE6Tun_To_KH up
```

**Step 4:**

**در سرور خارج :**


برای راه اندازی تانل IPIPv6 یا GRE6 در سرور خارج یکی از دو دستور زیر رو اجرا کنید :
>توجه داشته باشید هر تانلی رو که انتخاب میکنید دقیقا در سرور ایران هم همان تانل رو باید استفاده کنید .

## IPIPv6 :
```shell
ip -6 tunnel add ipip6Tun_To_IR mode ipip6 remote fc00::1 local fc00::2
ip addr add 192.168.13.2/30 dev ipip6Tun_To_IR
ip link set ipip6Tun_To_IR mtu 1440
ip link set ipip6Tun_To_IR up
```
## GRE6:
```shell
ip -6 tunnel add GRE6Tun_To_IR mode ip6gre remote fc00::1 local fc00::2
ip addr add 192.168.13.2/30 dev GRE6Tun_To_IR
ip link set GRE6Tun_To_IR mtu 1436
ip link set GRE6Tun_To_IR up
```


تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل IPIPv6 یا GRE6 بین سرور خارج و ایران برقرار شده .
- آیپی v4 لوکال سرور ایران : 192.168.13.1
- آیپی v4 لوکال سرور خارج : 192.168.13.2

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن آیپی v4 لوکال استفاده میکنیم : 
```shell
ping 192.168.13.2
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping 192.168.13.1
```
**تا اینجای کار تانل 6to4 برقرار شده و با استفاده از IP forward ترافیک رو به سمت تانلی که ساختیم میفرستیم :**

# فوروارد کردن ترافیک با IP forward :  (در سرور ایران)
```shell
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 192.168.13.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 192.168.13.2
iptables -t nat -A POSTROUTING -j MASQUERADE 
```




# نکنه :
بعد از ریبوت شدن سرور دستورات پاک میشوند ، در صورت نیاز مینوانید یک فایل ایجاد کنید و دستورات رو در داخل آن قرار دهید : 

**1. سرور ایران :**

- با دستور زیر فایل rc.local رو باز میکنیم 
```shell
sudo nano /etc/rc.local
```

- متن زیر را در فایل قرار میدیم و فایل رو ذخیره میکنیم : 
```shell
#! /bin/bash
ip tunnel add 6to4_To_KH mode sit remote IPv4-Kharej local IPv4-Iran
ip -6 addr add fc00::1/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up

ip -6 tunnel add ipip6Tun_To_KH mode ipip6 remote fc00::2 local fc00::1
ip addr add 192.168.13.1/30 dev ipip6Tun_To_KH
ip link set ipip6Tun_To_KH mtu 1440
ip link set ipip6Tun_To_KH up

sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 192.168.13.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 192.168.13.2
iptables -t nat -A POSTROUTING -j MASQUERADE 

exit 0
```

- در آخر دستور زیر رو اجرا میکنیم :
```shell
sudo chmod +x /etc/rc.local

```


**2. سرور خارج :**



- با دستور زیر فایل rc.local رو باز میکنیم 
```shell
sudo nano /etc/rc.local
```

- متن زیر را در فایل قرار میدیم و فایل رو ذخیره میکنیم : 
```shell
#! /bin/bash
ip tunnel add 6to4_To_IR mode sit remote ip-Iran local ip-Kharej
ip -6 addr add fc00::2/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up

ip -6 tunnel add ipip6Tun_To_IR mode ipip6 remote fc00::1 local fc00::2
ip addr add 192.168.13.2/30 dev ipip6Tun_To_IR
ip link set ipip6Tun_To_IR mtu 1440
ip link set ipip6Tun_To_IR up

exit 0
```

- در آخر دستور زیر رو اجرا میکنیم :
```shell
sudo chmod +x /etc/rc.local

```



# نحوه غیر فعال سازی تانل مابین سرور ها 

**Step i:** 

در سرور خارج کار های زیر رو انجام میدیم :‌

1. ابتدا دستور زیر رو اجرا میکنیم تا فایل rc.local رو حذف کنیم :
```shell
rm -f /etc/rc.local
```
2. حذف تونل 6to4 در سرور خارج:
```shell
ip tunnel del 6to4_To_IR
```
3. حذف تونل IPIPv6 یا GRE6 در سرور خارج:

برای IPIPv6:
```shell
ip -6 tunnel del ipip6Tun_To_IR
```
برای GRE6:
```shell
ip -6 tunnel del GRE6Tun_To_IR
```
4. سرور رو ریبوت میکنیم : 
```shell
reboot
```

**Step ii:** 

در سرور ایران کار های زیر رو انجام میدیم : 

1. جلوگیری از forward شدن ترافیک :
```shell
iptables -t nat -F
```

2. فایل rc.local رو حذف میکنیم :
```shell
rm -f /etc/rc.local
```

3. حذف تونل IPIPv6 یا GRE6 در سرور ایران:

برای IPIPv6:
```shell
ip -6 tunnel del ipip6Tun_To_KH
```
برای GRE6:
```shell
ip -6 tunnel del GRE6Tun_To_KH
```
4. سرور رو ریبوت میکنیم : 
```shell
reboot
```
 

اگر مراحل بالا به درستی انجام شوند، تونل‌ ایجاد شده حذف خواهد شد.
