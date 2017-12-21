A Service Bus-üzenetküldési entitások Azure-ban való használatának megkezdéséhez először létre kell hoznia egy, az Azure-ban egyedi névvel rendelkező névteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs ablaktábláján kattintson **+ hozzon létre egy erőforrást**, majd kattintson a **vállalati integrációs**, és kattintson a **Service Bus**.
3. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic, Standard vagy Prémium).
5. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
6. Az **Erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, amelynek a névtér a része lesz, vagy hozzon létre egy újat.      
7. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
8. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="obtain-the-management-credentials"></a>A felügyeleti hitelesítő adatok beszerzése
Az elsődleges és másodlagos kulcsok, hogy minden egyes teljes hozzáférés a névtér összes elemeit társított párjának automatikusan hozza létre egy új névtér hoz létre egy kezdeti közös hozzáférésű Jogosultságkód (SAS) szabály. Lásd: [Service Bus hitelesítési és engedélyezési](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) több további szabályok létrehozásával kapcsolatos információkat a korlátozott jogok rendszeres küldő és a. A kezdeti szabály másolásához kövesse az alábbi lépéseket: 

1.  Kattintson a **összes erőforrás**, majd kattintson az újonnan létrehozott névtér nevére.
2. A névtér ablakában kattintson **megosztott elérési házirendek**.
3. Az a **megosztott elérési házirendek** kattintson **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Az a **házirend: RootManageSharedAccessKey** ablak, kattintson a Másolás gombra a Tovább gombra **kapcsolati karakterlánc – elsődleges kulcs**, hogy másolja a kapcsolati karakterláncot a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string][connection-string]

5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
