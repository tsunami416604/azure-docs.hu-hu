### <a name="prerequisites"></a>Előfeltételek
Rendelkeznie kell egy [Service Bus](https://azure.microsoft.com/services/service-bus/) fiók.  

Azure Service Bus-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a service bus-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást csatlakozni a Service Bus-fiók engedélyezése lépései a következők:  

1. Válassza ki egy Service Bus-kapcsolat létrehozásához a logic app Designer **megjelenítése Microsoft felügyelt API-k** a legördülő listában. Írja be **service bus** be a keresőmezőbe. Válassza ki a eseményindító vagy a használni kívánt műveletet.  
    ![A Service Bus kapcsolati kép 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Ha még nem hozott létre a előtt a Service Bus-kapcsolatokat, akkor Service Bus-hitelesítő adatainak megadását kéri. Ezek a hitelesítő adatok segítségével engedélyezik a Logic Apps alkalmazást, és a Service Bus-fiókja adatainak eléréséhez. A Service Bus-összekötő a kapcsolódási karakterlánc szükséges a Service Bus-névtér. Is szükséges **kezelése** engedélyek. Jó tudni, hogy ha a kapcsolati karakterláncot a névtérhez, vagy egy adott entitás ha tartalmazza a `EntityPath` paraméter. Ha igen, nincs logikai alkalmazás megfelelő kapcsolati karakterláncát.  
    ![Service Bus kapcsolati karakterlánca](./media/connectors-create-api-servicebus/connectionstring.png)
3. Miután a névtérhez tartozó kapcsolati karakterláncot kapott, használhatja a Logic Apps API kapcsolat.  
    ![Kép: a Service Bus kapcsolat 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Figyelje meg, a kapcsolat létrejött, és most szabadon a Logic Apps alkalmazást a többi lépés folytatásához.  
    ![A Service Bus kapcsolati kép 3](./media/connectors-create-api-servicebus/servicebus-3.png)   

