Topology I will be using to do a basic setup of a router.
<img width="1080" height="738" alt="image" src="https://github.com/user-attachments/assets/d2c37e7a-b7a9-4727-856f-bd009a8f6b3a" />

First I had to power of the routers then add the wan cards to both  routers.
THen Add a Serial DCE CABLE to  connect both routers>

Serial Connections 
 There are two types DCE and DTE cables. In serial connections one end has to use one or the other. DCE(DATA COmmuncications Equipment) is 
 responsuble for providing clocking signal. Whish is what keeps data transmission synchronized.

 DTE (DATA Terminal Equipment) (add what this is AI)

 <img width="536" height="197" alt="image" src="https://github.com/user-attachments/assets/ecb9c41b-90e4-47ab-ba06-790a49033957" />


All the connections between interfaces oon routers and switches were done with copper over wire cables.

 Then i added the ip's tothe routers 

 <img width="648" height="601" alt="image" src="https://github.com/user-attachments/assets/1c7c882f-2491-40d6-99bd-a922ea4b563e" />

Next I had to configure the deadult gateways and IP addresses on PC's
<img width="619" height="138" alt="image" src="https://github.com/user-attachments/assets/7f607a18-5363-43ad-9f4e-0129c4098c85" />

<img width="822" height="332" alt="Screenshot 2026-06-30 145125" src="https://github.com/user-attachments/assets/3fca985b-a161-49a7-9507-0e19e97c72f2" />
<img width="877" height="285" alt="Screenshot 2026-06-30 145151" src="https://github.com/user-attachments/assets/68e40929-1568-455e-89f9-3d618276937e" />


Then Next i Had to configure a routing protocol on both routers using the network 10.0.0.0  and the two LAN's and used RIP v2:
<img width="536" height="426" alt="image" src="https://github.com/user-attachments/assets/82143cc7-e3f9-4234-9ca2-44ae707459e2" />


Then confirm i used show Ip route to check if it can find the necessart routes to devices
