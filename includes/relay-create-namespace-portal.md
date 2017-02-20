1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs paneljén kattintson az **Új**, majd a **Enterprise Integration** (Vállalati integráció), végül a **Service Bus** elemre.
3. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
4. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.
5. Az **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-portal.md)** mezőben válasszon ki egy meglévő erőforráscsoportot, amelynek a névtér a része lesz, vagy hozzon létre egy újat.      
6. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.
   
    ![Névtér létrehozása][create-namespace]
7. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Néhány perc múltán a rendszer kiosztja az erőforrásokat a fiókja számára.

### <a name="obtain-the-management-credentials"></a>A felügyeleti hitelesítő adatok beszerzése
1. A névterek listájában kattintson az újonnan létrehozott névtér nevére.
2. A névtér panelen kattintson a **Megosztott elérési házirendek** elemre.
3. A **Megosztott elérési házirendek** panelen kattintson a **RootManageSharedAccessKey** elemre.
   
    ![connection-info][connection-info]
4. A **Házirend: RootManageSharedAccessKey** panelen a **Kapcsolati karakterlánc – elsődleges kulcs** melletti másolás gombra kattintva másolja a kapcsolati karakterláncot a vágólapra későbbi használatra. Illessze be ezt az értéket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
   
    ![connection-string][connection-string]

5. A későbbi használat érdekében ismételje meg az előző lépést, és másolja ki és illessze be az **Elsődleges kulcs** értékét egy ideiglenes helyre.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com


<!--HONumber=Feb17_HO2-->


