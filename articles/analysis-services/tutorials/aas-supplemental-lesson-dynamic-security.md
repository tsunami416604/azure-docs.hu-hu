---
title: "Azure Analysis Services oktatóanyag – kiegészítő lecke: Dinamikus biztonság | Microsoft Docs"
description: "Ismerteti a dinamikus biztonság sorszűrőkkel való használatát az Azure Analysis Services oktatóanyagban."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 4e97a558ae1a2601b5275a73164b483351f03857
ms.contentlocale: hu-hu
ms.lasthandoff: 07/26/2017

---
# <a name="supplemental-lesson---dynamic-security"></a>Kiegészítő lecke – Dinamikus biztonság

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a kiegészítő leckében további, dinamikus biztonságot megvalósító szerepkört hoz létre. A dinamikus biztonság sorszintű biztonságot biztosít az aktuálisan bejelentkezett felhasználó felhasználóneve vagy bejelentkezési azonosítója alapján. 
  
A dinamikus biztonság megvalósítása érdekében hozzáadhat egy táblázatot az azon felhasználók felhasználóneveit tartalmazó modellhez, akik csatlakozhatnak a modellhez, és kereshetnek a modell objektumai és adatai között. Az ezen oktatóanyag segítségével létrehozott modell az Adventure Works környezetében található; azonban a lecke elvégzéséhez a saját tartományából származó felhasználókat tartalmazó táblázatot kell hozzáadnia. Nincs szükség a hozzáadott felhasználónevekhez tartozó jelszavakra. EmployeeSecurity tábla létrehozásához, amely a saját tartományából tartalmaz néhány felhasználót példaként, használhatja a Beillesztés funkciót, amellyel alkalmazotti adatokat illeszthet be egy Excel-táblázatból. A valós forgatókönyvekben a felhasználóneveket tartalmazó táblázat általában egy adatforrásként szolgáló valódi adatbázisból származna, például egy valódi DimEmployee táblából.  
  
A dinamikus biztonság beállításához két DAX-függvényt használhat: [USERNAME függvény (DAX)](http://msdn.microsoft.com/22dddc4b-1648-4c89-8c93-f1151162b93f) és [LOOKUPVALUE függvény (DAX)](http://msdn.microsoft.com/73a51c4d-131c-4c33-a139-b1342d10caab). Ezek a függvények a sorszűrő képletben alkalmazva egy új szerepkörben vannak meghatározva. A LOOKUPVALUE függvény használatával a képlet az EmployeeSecurity táblából ad meg egy értéket. Ezután a képlet továbbadja az értéket a USERNAME függvénynek, amely megadja az ehhez a szerepkörhöz tartozó, bejelentkezett felhasználó felhasználónevét. A felhasználó ezután csak a szerepkör sorszűrői által megadott adatokat böngészheti. Ebben a forgatókönyvben megadhatja, hogy az értékesítési alkalmazottak csak olyan értékesítési területek internetes értékesítési adatai között kereshetnek, amelyben tagok.  
  
Azok a feladatok, amelyek erre az Adventure Work táblázatos modellezési forgatókönyvre jellemzők, de nem feltétlenül vonatkoznak egy valós forgatókönyvre, ilyenként meg vannak jelölve. Mindegyik feladat a feladat célját leíró, kiegészítő információkat is tartalmaz.  
  
A lecke elvégzésének várható időtartama: **30 perc**.  
  
## <a name="prerequisites"></a>Előfeltételek  
Ez a kiegészítőlecke-témakör a táblázatos modellezésről szóló oktatóanyag része, amelyet sorrendben kell elvégezni. Az ebben a kiegészítő leckében található feladatok végrehajtása előtt be kell fejeznie minden előző leckét.  
  
## <a name="add-the-dimsalesterritory-table-to-the-aw-internet-sales-tabular-model-project"></a>A DimSalesTerritory tábla hozzáadása az AW internetes értékesítés táblázatos modellprojekthez  
A dinamikus biztonság beállításához ehhez az Adventure Works-forgatókönyvhöz való hozzá kell adnia két további táblázatot a modellhez. Az első hozzáadott táblázat a DimSalesTerritory (Értékesítési területként) ugyanabból az AdventureWorksDW-adatbázisból. Később sorszűrőt alkalmazhat a SalesTerritory táblára, amely meghatározza azokat az adatokat, amelyeket a bejelentkezett felhasználó kereshet.  
  
#### <a name="to-add-the-dimsalesterritory-table"></a>DimSalesTerritory tábla hozzáadása  
  
1.  A Táblázatos modelltallózó **Adatforrások** szakaszában kattintson a jobb gombbal a kapcsolatra, majd kattintson az **Új táblázatok importálása** gombra.  

    Ha megjelenik a Megszemélyesítési hitelesítő adatok párbeszédpanel, írja be a 2., Adatok hozzáadása című leckében használt megszemélyesítési hitelesítő adatokat.
  
2.  A Kezelőben válassza a **DimSalesTerritory** táblát, majd kattintson az **OK** lehetőségre.    
  
3.  A Lekérdezésszerkesztőben kattintson a **DimSalesTerritory** lekérdezésre, majd távolítsa el a **SalesTerritoryAlternateKey** oszlopot.  
  
7.  Kattintson az **Importálás** gombra.  
  
    Ezzel az új táblázat hozzáadódik a modell munkaterületéhez. A forrásként szolgáló DimSalesTerritory táblából származó objektumok és adatok ezután importálva lesznek az AW internetes értékesítés táblázatos modellbe.  
  
9. A táblázat sikeres importálása után kattintson a **Bezárás** gombra.  

## <a name="add-a-table-with-user-name-data"></a>A felhasználónevekre vonatkozó adatokat tartalmazó táblázat hozzáadása  
Az AdventureWorksDW-mintaadatbázisban található DimEmployee tábla az AdventureWorks tartomány felhasználóit tartalmazza. Ezek a felhasználónevek nem léteznek a saját környezetében. A modellben létre kell hoznia egy táblázatot, amely a szervezet aktuális felhasználói közül tartalmaz néhányat példaként (legalább hármat). Ezután tagként kell hozzáadnia ezeket a felhasználókat az új szerepkörhöz. Nincs szükség a hozzáadott minta-felhasználónevekhez tartozó jelszavakra, de szükség van létező, a saját környezetéből származó Windows-felhasználónevekre.  
  
#### <a name="to-add-an-employeesecurity-table"></a>EmployeeSecurity tábla hozzáadása  
  
1.  Nyissa meg a Microsoft Excelt, és hozzon létre egy munkalapot.  
  
2.  Másolja le a következő táblázatot a fejlécsorral együtt, majd illessze be a munkalapra.  

    ```
      |EmployeeId|SalesTerritoryId|FirstName|LastName|LoginId|  
      |---------------|----------------------|--------------|-------------|------------|  
      |1|2|<user first name>|<user last name>|\<domain\username>|  
      |1|3|<user first name>|<user last name>|\<domain\username>|  
      |2|4|<user first name>|<user last name>|\<domain\username>|  
      |3|5|<user first name>|<user last name>|\<domain\username>|  
    ```

3.  Cserélje ki az utónevet, a vezetéknevet és a tartomány/felhasználónevet a szervezet három felhasználójának nevére és bejelentkezési azonosítójára. Helyezze ugyanazt a felhasználót az EmployeeId 1 első két sorába, ezzel jelezve, hogy a felhasználó több értékesítési területhez tartozik. Az EmployeeId és a SalesTerritoryId mezőket ne módosítsa.  
  
4.  Mentse a munkalapot **SampleEmployee** néven.  
  
5.  A munkalapon válassza ki az alkalmazottak adatait tartalmazó összes cellát, beleértve a fejléceket is, majd kattintson a jobb gombbal a kiválasztott adatokra, és kattintson a **Másolás** gombra.  
  
6.  Az SSDT-ben kattintson a **Szerkesztés** menüre, majd a **Beillesztés** elemre.  
  
    Ha a Beillesztés gomb inaktív, kattintson bármely táblázat bármely oszlopába a modelltervező ablakban, majd próbálja meg újra.  
  
7.  A **Beillesztés villámnézete** párbeszédpanel **Táblázat neve** részbe írja be az **EmployeeSecurity** kifejezést.  
  
8.  A **Beillesztésre váró adatok** résznél ellenőrizze, hogy az adatok tartalmazzák-e az összes felhasználói adatot és fejlécet a SampleEmployee munkalapról.  
  
9. Ellenőrizze, hogy az **Első sor elemeinek használata oszlopfejlécként** be van-e jelölve, majd kattintson az **OK** gombra.  
  
    Létrejön egy EmployeeSecurity nevű, új tábla, amely a SampleEmployee munkalapról másolt alkalmazotti adatokat tartalmazza.  
  
## <a name="create-relationships-between-factinternetsales-dimgeography-and-dimsalesterritory-table"></a>Kapcsolatok létrehozása a FactInternetSales, a DimGeography és a DimSalesTerritory táblák között  
A FactInternetSales, a DimGeography és a DimSalesTerritory táblák mindegyike tartalmaz egy közös SalesTerritoryId nevű oszlopot. A DimSalesTerritory tábla SalesTerritoryId oszlopa különböző azonosítóval rendelkező értékeket tartalmaz minden értékesítési területhez.  
  
#### <a name="to-create-relationships-between-the-factinternetsales-dimgeography-and-the-dimsalesterritory-table"></a>Kapcsolatok létrehozásának módja a FactInternetSales, a DimGeography és a DimSalesTerritory táblázat között  
  
1.  A Diagramnézetben a **DimGeography** táblában kattintson a **SalesTerritoryId** oszlopra, és a gombot lenyomva húzza a mutatót a **DimSalesTerritory** tábla **SalesTerritoryId** oszlopára, majd engedje el a gombot.  
  
2.  A **FactInternetSales** táblában kattintson a **SalesTerritoryId** oszlopra, és a gombot lenyomva húzza a mutatót a **DimSalesTerritory** tábla **SalesTerritoryId** oszlopára, majd engedje el a gombot.  
  
    Vegye észre, hogy a kapcsolat Aktív tulajdonsága Hamis, ami inaktívat jelent. A FactInternetSales tábla már rendelkezik egy másik aktív kapcsolattal.  
  
## <a name="hide-the-employeesecurity-table-from-client-applications"></a>Az EmployeeSecurity tábla elrejtése az ügyfélalkalmazásokból  
Ebben a feladatban elrejtheti az EmployeeSecurity táblát, ezzel megakadályozva, hogy az megjelenjen az ügyfélalkalmazás mezőlistájában. Ne feledje, hogy a táblázat elrejtése nem nyújt a táblázat számára védelmet. A felhasználók továbbra is lekérdezhetik az EmployeeSecurity tábla adatait, ha tisztában vannak annak módjával. Az EmployeeSecurity tábla adatainak védelméhez, azaz annak megakadályozásához, hogy a felhasználók adatokat kérdezhessenek le a táblázatból, egy későbbi feladatban szűrőt alkalmazhat.  
  
#### <a name="to-hide-the-employeesecurity-table-from-client-applications"></a>Az EmployeeSecurity tábla elrejtése az ügyfélalkalmazásokból  
  
-   A modelltervező Diagramnézetében kattintson a jobb gombbal az **Alkalmazott** táblázatfejlécére, majd kattintson az **Elrejtés az ügyféleszközök elől** lehetőségre.  
  
## <a name="create-a-sales-employees-by-territory-user-role"></a>Területenkénti értékesítési alkalmazottak felhasználói szerepkör létrehozása  
Ebben a feladatban egy felhasználói szerepkört hozhat létre. Ez a szerepkör egy olyan sorszűrőt tartalmaz, amely meghatározza, hogy a DimSalesTerritory tábla mely sorait láthatják a felhasználók. A szűrő ezután alkalmazva lesz az egy-a-többhöz típusú kapcsolati irányban minden egyéb, a DimSalesTerritoryhoz kapcsolódó táblázathoz. Egy olyan szűrőt is alkalmazhat, amely az egész EmployeeSecurity táblát lekérdezhetetlenné teszi azon felhasználók számára, akik tagjai a szerepkörnek.  
  
> [!NOTE]  
> Az ebben a leckében létrehozott Területenkénti értékesítési alkalmazottak szerepkör azt a korlátozást tartalmazza, hogy a tagok csak arról az értékesítési területről kereshetnek (vagy kérdezhetnek le) értékesítési adatokat, amelybe tartoznak. Ha olyan felhasználót ad hozzá tagként a Területenkénti értékesítési alkalmazottak szerepkörhöz, aki már tagja egy, a [11. lecke: Szerepkörök létrehozása](../tutorials/aas-lesson-11-create-roles.md) részben létrehozott szerepkörnek, akkor az engedélyek kombinációját kapja. Ha a felhasználó egyszerre több szerepkörnek is tagja, akkor az engedélyek és az egyes szerepkörökhöz meghatározott sorszűrők összeadódnak. Ez azt jelenti, hogy a felhasználó a szerepkörök kombinációja által megadott nagyobb engedélyekkel rendelkezik.  
  
#### <a name="to-create-a-sales-employees-by-territory-user-role"></a>Területenkénti értékesítési alkalmazottak felhasználói szerepkör létrehozásának módja  
  
1.  Az SSDT-ben kattintson a **Modell** menüre, majd a **Szerepkörök** elemre.  
  
2.  A **Szerepkörkezelőben** kattintson az **Új** lehetőségre.  
  
    Egy új szerepkör Nincs engedéllyel hozzá lesz adva a listához.  
  
3.  Kattintson az új szerepkör elemre, majd a **Név** oszlopban nevezze át a szerepkört a következőre: **Területenkénti értékesítési alkalmazottak**.  
  
4.  Az **Engedélyek** oszlopban kattintson a legördülő listára, majd válassza az **Olvasás** engedélyt.  
  
5.  Kattintson a **Tagok** lapra, majd a **Hozzáadás** gombra.  
  
6.  A **Felhasználó vagy csoport kiválasztása** párbeszédpanelen az **Írja be a kijelölendő objektum nevét** mezőbe írja be az EmployeeSecurity tábla létrehozásakor használt első minta-felhasználónevet. Kattintson a **Névellenőrzés** lehetőségre a felhasználónév érvényességének ellenőrzéséhez, majd kattintson az **OK** gombra.  
  
    Ismételje meg ezt a lépést, hozzáadva a többi minta-felhasználónevet, amelyeket az EmployeeSecurity tábla létrehozása során használt.  
  
7.  Kattintson a **Sorszűrők** lapra.  
  
8.  Az **EmployeeSecurity** tábla a **DAX-szűrő** oszlopába írja be a következő képletet:  
  
    ```
      =FALSE()  
    ```
  
    Ez a képlet megadja, hogy minden oszlop feloldható a hamis logikai állapotra. Az EmployeeSecurity tábla egyik oszlopát sem kérdezhetik le a Területenkénti értékesítési alkalmazottak felhasználói szerepkör tagjai.  
  
9. A **DimSalesTerritory** táblába írja be a következő képletet:  

    ```  
    ='Sales Territory'[Sales Territory Id]=LOOKUPVALUE('Employee Security'[Sales Territory Id], 
      'Employee Security'[Login Id], USERNAME(), 
      'Employee Security'[Sales Territory Id], 
      'Sales Territory'[Sales Territory Id]) 
    ```
  
    Ebben a képletben a LOOKUPVALUE függvény a DimEmployeeSecurtiy[SalesTerritoryId] oszlop minden értékét visszaadja, ahol az EmployeeSecurity[LoginId] megegyezik az aktuálisan bejelentkezett Windows-felhasználónévvel, az EmployeeSecurity[SalesTerritoryId] pedig ugyanaz, mint a DimSalesTerritory[SalesTerritoryId].  
  
    A LOOKUPVALUE által visszaadott értékesítési területi azonosítók készletét ezután a DimSalesTerritory tábla sorainak korlátozására használja a rendszer. Csak azok a sorok jelennek meg, ahol a sorhoz tartozó SalesTerritoryID megtalálható a LOOKUPVALUE függvény által visszaadott azonosítók készletében.  
  
10. A Szerepkörkezelőben kattintson az **OK** gombra.  
  
## <a name="test-the-sales-employees-by-territory-user-role"></a>Területenkénti értékesítési alkalmazottak felhasználói szerepkör tesztelése  
Ebben a feladatban az SSDT-ben található Elemzés az Excelben szolgáltatást használhatja a Területenkénti értékesítési alkalmazottak felhasználói szerepkör hatékonyságának tesztelésére. Adja meg az EmployeeSecurity táblához hozzáadott és a szerepkör tagjává tett egyik felhasználónevet. Ez a felhasználónév hatályos felhasználónévként lesz használatos az Excel és a modell között létrehozott kapcsolatban.  
  
#### <a name="to-test-the-sales-employees-by-territory-user-role"></a>A Területenkénti értékesítési alkalmazottak felhasználói szerepkör tesztelése  
  
1.  Az SSDT-ben kattintson a **Modell** menüre, majd az **Elemzés az Excelben** elemre.  
  
2.  Az **Elemzés az Excelben** párbeszédpanelen az **Adja meg a modellhez való kapcsolódáshoz használandó felhasználónevet vagy szerepkört** részen válassza az **A Windows másik felhasználója** elemet, majd kattintson a **Tallózás** gombra.  
  
3.  A **Felhasználó vagy csoport kiválasztása** párbeszédpanelen az **Írja be a kijelölendő objektum nevét** mezőbe írja be azt a felhasználónevet, amelyet az EmployeeSecurity táblában adott meg, majd kattintson a **Nevek ellenőrzése** lehetőségre.  
  
4.  A **Felhasználó vagy csoport kiválasztása** párbeszédpanel bezárásához kattintson az **OK** gombra, majd kattintson ismét az **OK** gombra az **Elemzés az Excelben** párbeszédpanel bezárásához.  
  
    Megnyílik az Excel, és megjelenít egy új munkafüzetet. Ekkor automatikusan létrejön egy kimutatás. A Kimutatás mezők listája az új modellben elérhető legtöbb adatmezőt tartalmazza.  
  
    Vegye figyelembe, hogy az EmployeeSecurity tábla nem látható a Kimutatás mezők listájában. Ezt a táblázatot elrejtette az ügyféleszközök elől az előző feladatban.  
  
5.  A **Mezők** listában az **∑ Internetes értékesítés** (mértékek) részen válassza az **InternetTotalSales** mértéket. Ezt a mértéket az **Értékek** mezőkbe írja be a rendszer.  
  
6.  Válassza a **SalesTerritoryId** elemet a **DimSalesTerritory** táblából. A rendszer bejegyzi az oszlopot a **Sorfeliratok** mezőkbe.  
  
    Az internetes értékesítési adatok csak ahhoz a régióhoz jelennek meg, amelyhez a használt hatályos felhasználónév tartozik. Ha egy másik oszlopot választ ki, például a DimGeography tábla Város lehetőségét Sorfelirat-mezőként, akkor csak azok a városok jelennek meg, amelyek azon az értékesítési területen vannak, ahova a hatályos felhasználó tartozik.  
  
    Ez a felhasználó nem kereshet vagy kérdezhet le semmilyen internetes értékesítési adatot olyan területekről, amelyikhez nem tartozik. Ez a korlátozás azért áll fenn, mert a DimSalesTerritory táblához a Területenkénti értékesítési alkalmazottak felhasználói szerepkörben megadott sorszűrő védelmet biztosít az egyéb értékesítési területekhez kapcsolódó adatoknak.  
  
## <a name="see-also"></a>Lásd még:  
[USERNAME függvény (DAX)](https://msdn.microsoft.com/library/hh230954.aspx)  
[LOOKUPVALUE függvény (DAX)](https://msdn.microsoft.com/library/gg492170.aspx)  
[CUSTOMDATA függvény (DAX)](https://msdn.microsoft.com/library/hh213140.aspx)  
