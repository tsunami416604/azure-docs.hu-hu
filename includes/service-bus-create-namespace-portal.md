1. Jelentkezzen be az [Azure Portal][].

2. A portál bal oldali navigációs paneljén kattintson az **Új**, majd a **Enterprise Integration** (Vállalati integráció), végül a **Service Bus** elemre.

4. A **Névtér létrehozása** párbeszédpanelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.

5. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic, Standard vagy Prémium).

7. Az **Előfizetés** mezőben válassza ki azt az Azure-előfizetést, amelyben a névteret létre kívánja hozni.

9. Az **Erőforráscsoport** mezőben válasszon ki egy meglévő erőforráscsoportot, amelynek a névtér a része lesz, vagy hozzon létre egy újat.      

8. A **Hely** mezőben válassza ki azt az országot vagy régiót, amelyben a névtér üzemeltetve lesz.

    ![Névtér létrehozása][create-namespace]

6. Kattintson a **Létrehozás** gombra. A rendszer ekkor létrehozza és engedélyezi a névteret. Előfordulhat, hogy néhány percet várnia kell, amíg a rendszer kiosztja az erőforrásokat a fiókja számára.
 
### A felügyeleti hitelesítő adatok beszerzése

1. A névterek listájában kattintson az újonnan létrehozott névtér nevére.
 
3. A **Service Bus-névtér** panelen kattintson a **Megosztott elérési házirendek** elemre.

4. A **Megosztott elérési házirendek** panelen kattintson a **RootManageSharedAccessKey** elemre.

    ![connection-info][connection-info]

5. A **Házirend: RootManageSharedAccessKey** panelen a **Kapcsolati karakterlánc – elsődleges kulcs** melletti másolás gombra kattintva másolja a kapcsolati karakterláncot a vágólapra későbbi használatra.

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure Portal]: https://portal.azure.com


<!--HONumber=sep16_HO1-->


