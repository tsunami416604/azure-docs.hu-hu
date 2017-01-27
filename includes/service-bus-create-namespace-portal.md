## <a name="create-a-service-namespace"></a>Szolgáltatásnévtér létrehozása

A Service Bus-üzenetsorok Azure-ban való használatához először létre kell hoznia egy névteret. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül. 

Névtér létrehozása:

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs paneljén kattintson az **Új**, majd a **Enterprise Integration** (Vállalati integráció), végül a **Service Bus** elemre.
3. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic, Standard vagy Prémium).
5. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
6. Az **Erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, amelynek a névtér a része lesz, vagy hozzon létre egy újat.      
7. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
8. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="obtain-the-management-credentials"></a>A felügyeleti hitelesítő adatok beszerzése
1. A névterek listájában kattintson az újonnan létrehozott névtér nevére.
2. A névtér panelen kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** panelen kattintson a **RootManageSharedAccessKey** elemre.
   
    ![connection-info][connection-info]
4. A **Házirend: RootManageSharedAccessKey** panelen a **Kapcsolati karakterlánc – elsődleges kulcs** melletti másolás gombra kattintva másolja a kapcsolati karakterláncot a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure portal]: https://portal.azure.com

<!--HONumber=Jan17_HO3-->


