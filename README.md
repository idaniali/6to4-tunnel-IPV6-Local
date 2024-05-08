# فعال سازی 6to4 tunnel IPV6 Local بدون نیاز به IPv6 

> Disclaimer: This project is only for personal learning and communication, please do not use it for illegal purposes, please do not use it in a production environment
> 
>این پروژه فقط برای یادگیری و ارتباط شخصی است، لطفا از آن برای مقاصد غیرقانونی استفاده نکنید.


اگر این پروژه برای شما مفید بود با ستاره دادن ⭐ و دونیت کردن از من حمایت کنید تا باعث انگیزه و ادامه دادن بنده در این مسیر شود .

- USDT و TRX (TRC20): `TG4fcAJoMcVjV8iaKFmC6AGxkhR15nMwcH`
- BTC : `bc1qpme6r9fn6yc95wcu5gawwtunzgfg2w24022gld`
- DOGE : `DMS6zp2QPmpfL9Yivhrk4iaYb1F9eF7G7S`
- Ethereum (ETH) : `0xbcBD3eEAa7FDA96dc0773cFd11a07a6B368AA95A`

**کوچک ترین لطفی که به خودتون میتونید بکنید این هست که حداقل دوره Network+ رو گذونده باشید تا با آدرسی دهی های ipv4 و ipv6 اشنایی داشته باشید.**



**توضیحاتی درباره رنج های IPv4 و IPv6 در این سایت هست :** [کلیک کنید](https://en.wikipedia.org/wiki/Reserved_IP_addresses)

**Step 0:** 

آیپی های استفاده شده در آموزش سمپل هستند و قبل از هر کاری از سایت زیر آیپی v6 لوکال دریافت کنید .
  - دریافت IPv6 لوکال : [کلیک کند](https://www.unique-local-ipv6.com/#)
  - به دلخواه بعد از :: مقداری بین 0-9 و a-f (هگزادسیمال) را وارد کنید .
  - به عنوان مثال fde8:b030:25cf::/64 این آیپی از سایت زیر دریافت شده و دو ایپی به دلخواه میسازیم :
  - IP 1 : fde8:b030:25cf::de01/64
  - IP 2 : fde8:b030:25cf::de02/64



**Step 1:** 

ابتدا در سرور ایران باید این دستورات رو وارد کنیم تا تانل 6to4 در سرور ایران ، برقرار شود .
>راهنمایی : (در قسمت `<IPv4-KHAREJ>` ، آیپی ورژن 4 پابلیک سرور خارج رو قرار میدیم و در بخش `<IPv4-IRAN>` ، آیپی ورژن 4 پابلیک ایران رو قرار میدیم)
```shell
ip tunnel add 6to4_To_KH mode sit remote <IPv4-KHAREJ> local <IPv4-IRAN>
ip -6 addr add fde8:b030:25cf::de01/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up
```



**Step 2:** 

در این مرحله تانل 6to4 رو در سرور خارج برقرار میکنیم .
>راهنمایی : (در قسمت `<IPv4-KHAREJ>` ، آیپی ورژن 4 پابلیک سرور خارج رو قرار میدیم و در بخش `<IPv4-IRAN>` ، آیپی ورژن 4 پابلیک ایران رو قرار میدیم)

```shell 
ip tunnel add 6to4_To_IR mode sit remote <IPv4-IRAN> local <IPv4-KHAREJ>
ip -6 addr add fde8:b030:25cf::de02/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up
```
تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل 6to4 بین سرور خارج و ایران برقرار شده .
- آیپی v6 سرور ایران : fde8:b030:25cf::de01
- آیپی v6 سرور خارج : fde8:b030:25cf::de02

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن IPv6 استفاده میکنیم : 
```shell
ping6 fde8:b030:25cf::de02
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping6 fde8:b030:25cf::de01
```


# فعال سازی تانل GRE6 بر روی تانل 6TO4

**Step 3:**

**در سرور ایران :**


```shell
ip -6 tunnel add GRE6Tun_To_KH mode ip6gre remote fde8:b030:25cf::de02 local fde8:b030:25cf::de01
ip addr add 172.20.20.1/30 dev GRE6Tun_To_KH
ip link set GRE6Tun_To_KH mtu 1436
ip link set GRE6Tun_To_KH up
```

**Step 4:**

**در سرور خارج :**


```shell
ip -6 tunnel add GRE6Tun_To_IR mode ip6gre remote fde8:b030:25cf::de01 local fde8:b030:25cf::de02
ip addr add 172.20.20.2/30 dev GRE6Tun_To_IR
ip link set GRE6Tun_To_IR mtu 1436
ip link set GRE6Tun_To_IR up
```


تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل GRE6 بین سرور خارج و ایران برقرار شده .
- آیپی v4 لوکال سرور ایران : 172.20.20.1
- آیپی v4 لوکال سرور خارج : 172.20.20.2

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن آیپی v4 لوکال استفاده میکنیم : 
```shell
ping 172.20.20.2
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping 172.20.20.1
```
**تا اینجای کار تانل 6to4 برقرار شده و با استفاده از IP forward ترافیک رو به سمت تانلی که ساختیم میفرستیم :**

# فوروارد کردن ترافیک با IP forward :  (در سرور ایران)
```shell
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 172.20.20.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 172.20.20.2
iptables -t nat -A POSTROUTING -j MASQUERADE 
```




# ‌‌‌‌‌‌ذخیره کردن تانل و فعال سازی خودکار زمانی که سیستم ری استارت میشود  :
بعد از ریبوت شدن سرور دستورات پاک میشوند ، در صورت نیاز مینوانید از دستورات زیر استفاده کنید : 

**1. سرور ایران :**

- با دستور زیر فایل rc.local رو باز میکنیم 
```shell
sudo nano /etc/rc.local && sudo chmod +x /etc/rc.local
```

- متن زیر را در فایل قرار میدیم و فایل رو ذخیره میکنیم : 
```shell
#! /bin/bash
ip tunnel add 6to4_To_KH mode sit remote <IPv4-KHAREJ> local <IPv4-IRAN>
ip -6 addr add fde8:b030:25cf::de01/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up

ip -6 tunnel add GRE6Tun_To_KH mode ip6gre remote fde8:b030:25cf::de02 local fde8:b030:25cf::de01
ip addr add 172.20.20.1/30 dev GRE6Tun_To_KH
ip link set GRE6Tun_To_KH mtu 1436
ip link set GRE6Tun_To_KH up

sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 172.20.20.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 172.20.20.2
iptables -t nat -A POSTROUTING -j MASQUERADE 

exit 0
```

**2. سرور خارج :**



- با دستور زیر فایل rc.local رو باز میکنیم 
```shell
sudo nano /etc/rc.local && sudo chmod +x /etc/rc.local
```

- متن زیر را در فایل قرار میدیم و فایل رو ذخیره میکنیم : 
```shell
#! /bin/bash
ip tunnel add 6to4_To_IR mode sit remote <IPv4-IRAN> local <IPv4-KHAREJ>
ip -6 addr add fde8:b030:25cf::de02/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up

ip -6 tunnel add GRE6Tun_To_IR mode ip6gre remote fde8:b030:25cf::de01 local fde8:b030:25cf::de02
ip addr add 172.20.20.2/30 dev GRE6Tun_To_IR
ip link set GRE6Tun_To_IR mtu 1436
ip link set GRE6Tun_To_IR up

exit 0
```

بعد از reboot کردن سیستم تغییرات اعمال خواهند شد .

# پاک کردن تانل :
**در سرور خارج :**
ابتدا فایل rc.local رو با دستور زیر پاک کنید :
```
rm -r rc.local
```
و سپس :

```shell
ip tunnel del 6to4_To_IR
ip tunnel del GRE6Tun_To_IR
```


**در سرور ایران :**

ابتدا فایل rc.local رو با دستور زیر پاک کنید :
```
rm -r rc.local
```
و سپس :

```shell
ip tunnel del 6to4_To_KH
ip tunnel del GRE6Tun_To_KH
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

