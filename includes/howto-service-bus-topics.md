## Mik azok a Service Bus-üzenettémák és -előfizetések?

A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/howto-service-bus-topics/sb-topics-01.png)

A Service Bus-üzenetsorokkal ellentétben, amely esetében minden üzenetet egy-egy fogyasztó dolgoz fel, az üzenettémák és előfizetések a közzététel/előfizetés minta használatával „egy a többhöz” típusú kommunikációt biztosítanak. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Lehetősége van szűrőszabályok előfizetésenként történő beállítására is, amelyek lehetővé teszik annak szűrését vagy korlátozását, hogy az adott témakörbe beérkezett üzenetet melyik előfizetések kaphatják meg.

A Service Bus-üzenettémák és -előfizetések lehetővé teszik a számos felhasználótól és alkalmazásból érkező, hatalmas mennyiségű üzenet méretezését és feldolgozását.

## Névtér létrehozása

A Service Bus-üzenettémák és -előfizetések Azure-ban való használatához először létre kell hoznia egy *szolgáltatásnévteret*. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak kezeléséhez az alkalmazáson belül.

Névtér létrehozása:

1.  Jelentkezzen be a [klasszikus Azure portálra][].

2.  A portál bal oldali navigációs panelén kattintson a **Service Bus** elemre.

3.  A portál alsó panelén kattintson a **Létrehozás** elemre.   
    ![][0]

4.  Az **Új névtér hozzáadása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.   
    ![][2]

5.  Miután meggyőződött arról, hogy a név elérhető, válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz (ugyanazt azt az országot/régiót kell használnia, amelyben a számítási erőforrásokat üzembe helyezi).

    > [AZURE.IMPORTANT] **Ugyanazt a régiót** válassza, amelyet az alkalmazás telepítéséhez kíván használni. Ez biztosítja a legjobb teljesítményt.

6.  A párbeszédpanelen lévő többi mezőt hagyja az alapértelmezett értékükön (**Üzenetküldés** és **Standard csomag**), majd kattintson az OK pipára. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

    ![][6]

## Az alapértelmezett felügyeleti hitelesítő adatok beszerzése a névtérhez

Ha kezelési műveleteket szeretne végrehajtani az új névtéren, például egy üzenettémakört vagy előfizetést szeretne létrehozni, be kell szereznie a névtérhez tartozó felügyeleti hitelesítő adatokat. Ezeket a hitelesítő adatokat a portálról szerezheti be.

### A felügyeleti hitelesítő adatok beszerzése a portálról

1.  A bal oldali navigációs panelen kattintson a **Service Bus** csomópontra az elérhető névterek listájának megjelenítéséhez.   
    ![][0]

2.  A megjelenő listában válassza ki az imént létrehozott névteret:   
    ![][3]

3.  Kattintson a **Kapcsolatadatok** gombra.   
    ![][4]

4.  Az **Access connection information** (Kapcsolatadatok hozzáférése) párbeszédablakban keresse meg azt a kapcsolati karakterláncot, amely tartalmazza az SAS-kulcsot és annak nevét. Jegyezze fel ezeket az értékeket, mert később szüksége lesz rájuk a névtérrel kapcsolatos műveletek végrehajtásához. 


  [klasszikus Azure portálra]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png




<!--HONumber=Jun16_HO2-->


