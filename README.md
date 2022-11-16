# ccna-project
ospf configration for different network 
Öncelikle portlarımızı ip adresleriyle  yapılandırmasını ve  cihazlarımızın ip adlerinin girilmesiyle başlıyoruz.
ROUTER 0
İnt fa0/0
İp add 192.168.1.1  255.255.255.0
No sh

İnt s0/0/0
İp add 11.0.0.1  255.255.255.252
No sh

İnt s0/0/1
İp add 13.0.0.1  255.255.255.252
No sh

ROUTER 1
İnt s0/0/0
İp add 11.0.0.2  255.255.255.252
No sh

İnt fa0/0
İp add 192.168.2.1 255.255.255.0
No sh


ROUTER 2
İnt s0/0/0
İp add 13.0.0.2  255.255.255.252
No sh
İnt fa0/0
İp add 192.168.3.1 255.255.255.0
No sh

Daha sonra topolojimizdeki networklerin birbirleriyle haberleşebilmesi için dynamic routing protokollerinden OSPF ile yapılandırlmamız gerekiyor

ROUTER 0
 Router ospf 1
Network 192.168.1.0  0.0.0.255 area 0
Network 11.0.0.0   0.0.0.3   area 0
Network 13.0.0.0   0.0.0.3  area 0


ROUTER 1
Router ospf 1
Network 192.168.2.0   0.0.0.255  area 0
Network 11.0.0.0  0.0.0.3  area 0

ROUTER 2
Router ospf 1
Network 192.168.3.0  0.0.0.255  area 0
Network 13.0.0.0  0.0.0.3  area 0
 
Ve son olarak 192.168.2.2 ve 192.168.3.2  cihazları DNS sunucusuna udp 53 portundan,WEB sunucusuna da tcp 80 portundan erişimi için out yönünde ACL yapılandırması yapıyoruz.

ROUTER 0
İp Access-list extended WEBACL 
Permit udp any host 192.168.1.2 eq 53
Permit tcp any host 192.168.1.3 eq 80
İnt fa0/0
İp Access-group WEBACL out

ACL çalışma mantığı gereği izin vermediğimiz bütün trafiği bloklamaktadır.Yazdığımız ACL de 192.168.1.2 ip’si için DNS erişimi,192.168.1.3 ip’si için HTTP erişimi verilmiştir. 


İpv6  Yapılandırması
İpv4 ile yaptığımız konfigürasyonu ipv6 ile aşağıdaki gibi yapıyoruz.Burada dikkat etmemiz gereken routerlara 
ipv6 unicast-routing komutunu eklememiz gerekiyor.

ROUTER 0
İpv6 unicast-routing
İnt fa0/0
İpv6 add 1ef0:111:11:1::1/64
İnt s0/0/0
İpv6 add 1ef0:abc:bc:c::1/126
İnt s0/0/1
İpv6 add 1ef0:def:ef:f::1/126

Router 1
İpv6 unicast-routing
İnt s0/0/0
İpv6 add 1ef0:abc:bc:c::2/126
İnt fa0/0
İpv6 add 1ef0:222:22:2::1/64

Router 2
İpv6 unicast-routing
İnt s0/0/0
İpv6 add 1ef0:def:ef:f::2/126
İnt fa0/0
İpv6 add 1ef0:333:33:3::1/64
Projede ipv4 adresleri kaldırılması durumunda projemizin ipv6 ile çalışabilmesi için  ipv6 ospf yapılandırmasında router-id eklemiz gerekiyor.
Router 0
İpv6 router ospf 1
Router-id 1.1.1.1
İnt fa0/0
İpv6 ospf 1 area 0
İnt s0/0/0
İpv6 ospf 1 area 0
İnt s0/0/1
İpv6 ospf 1 area 0

Router 1
İpv6 router ospf 1
Router-id 2.2.2.2
İnt s0/0/0
İpv6 ospf 1 area 0
İnt fa0/0
İpv6 ospf 1 area 0

Router 2
İpv6 router ospf 1
Router-id 3.3.3.3
İnt s0/0/0
İpv6 ospf 1 area 0
İnt fa0/0
İpv6 ospf 1 area 0

ROUTER 0
İpv6 Access-list WEBACL6 
Permit udp any host 1ef0:111:11:1::2 eq 53
Permit tcp any host 1ef0:111:11:1::3  eq 80
İnt fa0/0
İpv6 traffic-filter WEBACL6 out

Böylece proje hem ipv4 hem de ipv6 ile konfigüre edilmiştir.




