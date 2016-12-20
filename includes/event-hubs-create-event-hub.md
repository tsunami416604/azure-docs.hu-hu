## <a name="create-an-event-hub"></a>Eseményközpont létrehozása
1. Jelentkezzen be az [Azure Portal][Azure Portal], és kattintson az **Új** gombra a képernyő bal felső részén.
2. Kattintson az **Eszközök internetes hálózata**, majd az **Event Hubs** lehetőségre.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub9.png)
3. A **Névtér létrehozása** panelen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub1.png)
4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic vagy Standard). Valamint válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni. 
5. A névtér létrehozásához kattintson a **Létrehozás** parancsra.
6. Az Event Hubs névtérlistájában kattintson az újonnan létrehozott névtérre.      
   
    ![](./media/event-hubs-create-event-hub/create-event-hub2.png)
7. A névtér panelen kattintson az **Event Hubs** elemre.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub3.png)
8. A panel tetején kattintson az **Eseményközpont felvétele** parancsra.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub4.png)
9. Adjon egy nevet az Eseményközpontnak, majd kattintson a **Létrehozás** parancsra.
   
    ![](./media/event-hubs-create-event-hub/create-event-hub5.png)
10. Az eseményközpontok listájában kattintson az újonnan létrehozott eseményközpont nevére. 
    
     ![](./media/event-hubs-create-event-hub/create-event-hub6.png)
11. Térjen vissza a névtér panelre (nem az adott eseményközpont panelére), kattintson a **Megosztott elérési házirendek**, majd a **RootManageSharedAccessKey** elemre.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub7.png)
12. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati karakterláncot a vágólapra másolja. Mentse el ezt a kapcsolati karakterláncot a jelen oktatóprogramban való későbbi használat céljából.
    
     ![](./media/event-hubs-create-event-hub/create-event-hub8.png)

Létrejött az Eseményközpont, és most már rendelkezik az események küldéséhez és fogadásához szükséges kapcsolati karakterlánccal.

[Azure Portal]: https://portal.azure.com/

<!--HONumber=Nov16_HO4-->


