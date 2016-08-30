<properties
  pageTitle="Azure IoT Suite – gyakran ismételt kérdések | Microsoft Azure"
  description="Gyakran ismételt kérdések az IoT Suite-ról"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/09/2016"
  ms.author="araguila"/>
   
# Gyakran ismételt kérdések az IoT Suite-ról

### Mi a különbség egy erőforráscsoport Azure Portalról való törlése és azureiotsuite.com webhelyen egy előre konfigurált megoldásban a törlés gombra való kattintás között?

- Ha törli az előre konfigurált megoldást az [azureiotsuite.com][lnk-azureiotsuite] webhelyen, törli az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást. Ha további erőforrásokat adott az erőforráscsoporthoz, ezeket is törli. 

- Ha az erőforráscsoportot törli az [Azure Portalon][lnk-azure-portal], csak az erőforráscsoportban lévő erőforrásokat törli. Az előre konfigurált megoldással társított Azure Active Directory-alkalmazást is törölnie kell a [klasszikus Azure portálon][lnk-classic-portal].

### Hány DocumentDB -példányt adhatok meg egy előfizetésben?

Ötöt. Létrehozhat egy [Azure támogatási jegyet][link-azuresupportticket] a korlát növeléséhez, de alapértelmezés szerint csak öt DocumentDB-példányt adhat meg előfizetésenként. Ennek eredményeképpen csak legfeljebb öt előre konfigurált távoli figyelési megoldást adhat meg egy adott előfizetésben.

### Hány ingyenes Bing Térképek API-t adhatok meg egy előfizetésben?

Kettőt. Csak két ingyenes Bing Térképek API-t hozhat létre egy előfizetésben. Az ingyenes Bing térképek API-kkal a rendszer alapértelmezés szerint kiépíti a távoli figyelési megoldást. Ezért csak legfeljebb két távoli figyelő megoldást adhat meg a nem módosított előfizetésekben.

### Statikus térképpel rendelkező távoli figyelő megoldásom van, hogyan adhatok hozzá interaktív Bing-térképet? 
1. Az [Azure Portalról][lnk-azure-portal] szerezheti be a Vállalati Bing Térképek API QueryKey kulcsát: 
 1. Keresse meg a Vállalati Bing Térképek API-t tartalmazó erőforráscsoportot az [Azure Portalon][lnk-azure-portal].
 2. Kattintson az Összes beállítás, majd a Kulcskezelés elemre. 
 3. Két kulcsot láthat: a MasterKey és a QueryKey kulcsot. Másolja a QueryKey értékét.

     > [AZURE.NOTE] Nem rendelkezik Vállalati Bing Térképek API-fiókkal? Az [Azure Portalon][lnk-azure-portal] hozhatja létre a + Új elemre kattintva, a Vállalati Bing Térképek API megkeresésével, majd a létrehozási utasítások követésével.

2. Kérje le a legújabb kódot az [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] helyről.

3. Futtasson helyi vagy felhőbeli telepítést a tárház /docs/ mappájában található parancssori üzembehelyezési útmutató alapján. 

4. Helyi vagy felhőbeli telepítés futtatása után keresse meg a gyökérmappában az üzembe helyezés alatt létrehozott *.user.config fájlt. Nyissa meg ezt a fájlt egy szövegszerkesztőben. 

5. Módosítsa úgy a következő sort, hogy tartalmazza a QueryKey másolt értékét: 
   
  `<setting name="MapApiQueryKey" value="" />`

### Létrehozhatok előre konfigurált megoldást, ha Microsoft Azure for DreamSpark-fiókom van?
Jelenleg nem hozhat létre előre konfigurált megoldást [Microsoft Azure for DreamSpark][lnk-dreamspark]-fiókkal. Létrehozhat azonban egy [ingyenes Azure-próbafiókot][lnk-30daytrial] csupán néhány perc alatt, amellyel előre konfigurált megoldást hozhat létre.

### Hogyan törölhetek egy AAD-bérlőt?

Lásd Eric Golpe blogbejegyzését [az Azure AD-bérlők törléséről][lnk-delete-aad-tennant].

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=jun16_HO2-->


