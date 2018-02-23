## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/howto-service-bus-topics/sb-topics-01.png)

Ellentétben a Service Bus-üzenetsorok, ahol minden üzenetet dolgoz fel egy-egy fogyasztó, témakörök és előfizetések adja meg a kommunikáció közzététel/előfizetés minta használatával "egy a többhöz" űrlap. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Akkor is szűrőszabályok előfizetésenként alapon témakör. Állapotszűrő szabályok lehetővé teszik szűrését vagy korlátozását, mely üzeneteket a témakörökbe melyik előfizetések kaphatják érkezik.

Service Bus-üzenettémák és előfizetések lehetővé teszik méretezését és feldolgozását üzenetek nagy számú számos felhasználótól és alkalmazásokat.

## <a name="create-a-namespace"></a>Névtér létrehozása
A Service Bus-üzenettémák és -előfizetések Azure-ban való használatához először létre kell hoznia egy *szolgáltatásnévteret*. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs ablaktábláján kattintson **hozzon létre egy erőforrást**, majd kattintson a **vállalati integrációs**, és kattintson a **Service Bus**.
3. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic, Standard vagy Prémium).
5. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
6. Az a **erőforráscsoport** mezőben válasszon egy meglévő erőforráscsoportot, ahol a névtérben található, vagy hozzon létre egy újat.      
7. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
8. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="obtain-the-credentials"></a>Hitelesítő adatok beszerzése
1. A névterek listájában kattintson az újonnan létrehozott névtér nevére.
2. Az a **Service Bus-névtér** ablaktáblán kattintson a **megosztott elérési házirendek**.
3. Az a **megosztott elérési házirendek** ablaktáblán kattintson a **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Az a **házirend: RootManageSharedAccessKey** ablaktáblában a Másolás gombra kattint, a Tovább gombra kattintással **kapcsolati karakterlánc – elsődleges kulcs**, hogy másolja a kapcsolati karakterláncot a vágólapra későbbi használatra.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


