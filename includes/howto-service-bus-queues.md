## Mik azok a Service Bus-üzenetsorok?

A Service Bus-üzenetsorok **közvetítőalapú üzenettovábbítási** kommunikációs modellt biztosítanak az üzenettovábbításhoz. Üzenetsorok használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenetsoron keresztül. Az üzenet létrehozója (a küldő) átadja az üzenetet az üzenetsornak, majd folytatja annak feldolgozását. Aszinkron módon az üzenetfogyasztó (a fogadó) kiemeli az üzenetet az üzenetsorból, és feldolgozza azt. A küldőnek nem kell várnia a fogyasztó válaszára a további üzenetek feldolgozásához és kiküldéséhez. Az üzenetsorok **elsőnek be, elsőnek ki (First In, First Out, FIFO)** üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Ez azt jelenti, hogy az üzeneteket általában az érzékelők fogadják és dolgozzák fel a sorhoz történő hozzáadásuk sorrendjében, és minden üzenetet csak egy üzenetfogyasztó fogad és dolgoz fel.

![Az üzenetsorok alapfogalmai](./media/howto-service-bus-queues/sb-queues-08.png)

A Service Bus-üzenetsor egy olyan általános célú technológia, amely széles körben felhasználható:

-   Egy többszintes Azure-alkalmazásban, a webes és feldolgozói szerepkörök közötti kommunikáció során.
-   Egy hibrid megoldásban, a helyszíni és az Azure által kezelt alkalmazások közötti kommunikáció során.
-   A különböző szervezetekben, vagy egy szervezet különböző részlegein helyileg futó elosztott alkalmazások összetevői közötti kommunikáció során.

Az üzenetsorok használata lehetővé teszi az alkalmazások könnyebb méretezését, valamint nagyobb mértékű rugalmasságot biztosít az architektúrák számára.

## Szolgáltatásnévtér létrehozása

A Service Bus-üzenetsorok Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak kezeléséhez az alkalmazáson belül.

Névtér létrehozása:

1.  Jelentkezzen be a [klasszikus Azure portálra][].

2.  A portál bal oldali navigációs panelén kattintson a **Service Bus** elemre.

3.  A portál alsó panelén kattintson a **Létrehozás** elemre.
    ![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Az **Új névtér hozzáadása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.   
    ![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Miután meggyőződött arról, hogy a név elérhető, válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz (ugyanazt azt az országot/régiót kell használnia, amelyben a számítási erőforrásokat üzembe helyezi).

     > [AZURE.IMPORTANT] **Ugyanazt a régiót** válassza, amelyet az alkalmazás telepítéséhez kíván használni. Ez biztosítja a legjobb teljesítményt.

6.  A párbeszédpanelen lévő többi mezőt hagyja az alapértelmezett értékükön (**Üzenetküldés** és **Standard csomag**), majd kattintson az OK pipára. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

    ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

A létrehozott névtér aktiválása eltarthat egy rövid ideig. Az aktiválás befejeztével a névtér megjelenik a portálon. A folytatás előtt várja meg, amíg a névtér állapota **Aktív** nem lesz.

## Az alapértelmezett felügyeleti hitelesítő adatok beszerzése a névtérhez

Ha kezelési műveleteket szeretne végrehajtani az új névtéren, például egy üzenetsort szeretne létrehozni, be kell szereznie a névtérhez tartozó felügyeleti hitelesítő adatokat. Ezeket a hitelesítő adatokat a [klasszikus Azure portálra][] szerezheti be.

###A felügyeleti hitelesítő adatok beszerzése a portálról

1.  A bal oldali navigációs panelen kattintson a **Service Bus** csomópontra az elérhető névterek listájának megjelenítéséhez:   
    ![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  A megjelenő listában válassza ki az imént létrehozott névteret:   
    ![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Kattintson a **Kapcsolatadatok** gombra.   
    ![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  Az **Access connection information** (Kapcsolatadatok hozzáférése) panelen keresse meg azt a kapcsolati karakterláncot, amely tartalmazza az SAS-kulcsot és annak nevét.   

    ![](./media/howto-service-bus-queues/multi-web-45.png)
    
5.  Jegyezze fel kulcs értékét, vagy másolja a vágólapra.

  [klasszikus Azure portálra]: http://manage.windowsazure.com




<!--HONumber=Jun16_HO2-->


