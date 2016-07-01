<properties 
    pageTitle="Mi a Logic Apps szolgáltatás?" 
    description="További tudnivalók az App Service Logic Apps szolgáltatásról" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="04/07/2016"
    ms.author="klam"/>

#Mi a Logic Apps szolgáltatás?

| Rövid összefoglalás |
| --------------- |
| [A Logic Apps szolgáltatás definíciós nyelve](https://msdn.microsoft.com/library/azure/mt643789.aspx) |
| [A Logic Apps-összekötők dokumentációja](../connectors/apis-list.md) |
| [Logic Apps-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) |

Az Azure App Service teljes körűen felügyelt platformszolgáltatás fejlesztők számára, amely jelentősen megkönnyíti a webes, mobilos és integrációs alkalmazások megalkotását. A Logic Apps ennek a programcsomagnak a része. A szolgáltatás segítségével a technikusok vagy fejlesztők egy könnyen használható vizuális tervezőeszközzel automatizálhatják az üzleti folyamatok végrehajtását és az ezekhez kapcsolódó munkafolyamatokat.

A Logic Apps ráadásul a beépített [felügyelt API-kkal][felügyelt api-k] együtt használva még a legbonyolultabb integrációs kihívásokra is egyszerű választ tud adni: 

![Alkalmazásfolyamat-tervező](./media/app-service-logic-what-are-logic-apps/LogicAppCapture2.png)

Ahogy korábban már említettük, a logikai alkalmazások segítségével automatizálhatja az üzleti folyamatokat. Íme néhány példa erre:  
 
* Beállíthatja, hogy a rendszer automatikusan replikálja az SQL-adatbázisba bekerülő új bejegyzéseket, majd e-mailt küldjön azokról az ügyfélszolgálatnak.   
* Beállíthatja, hogy a rendszer automatikusan megkeresse a negatív Twitter-üzeneteket, majd továbbítsa őket egy Slack-csatornára.

Az ehhez hasonló helyzeteknek megfelelő konfiguráláshoz mindössze a vizuális tervező szükséges, egyetlen sor kódot sem kell írnia. Kezdje el [összeállítani saját logikai alkalmazását][létrehozás].

## Miért válasszam a Logic Apps szolgáltatást?

A Logic Apps szolgáltatásban a fejlesztők olyan munkafolyamatokat alkothatnak meg, amelyek egy eseményindítóval kezdődnek, majd végrehajtanak bizonyos lépéseket. A rendszer minden egyes lépésnél elindít egy-egy API-t, illetve biztonságosan gondoskodik a hitelesítésről és a további ajánlott eljárásokról, például az ellenőrzőpontokról és a tartós végrehajtásról.

A Logic Apps remek megoldást nyújt a különféle adatforrások integrálására, akár a felhőtől kezdve a helyszíni erőforrásokig. Ez különösen hasznos lehet, ha üzleti folyamatokat szeretne automatizálni (például megkeresni a negatív Twitter-üzeneteket, majd elküldeni őket egy belső Slack-csatornára, vagy a beérkezést követően replikálni az új ügyfélbejegyzéseket az SQL-ből a vállalati CRM-rendszerbe). Ha további lehetőségekre kíváncsi, olvasson a [felügyelt API-król][felügyelt api-k], vagy [vágjon bele már most][létrehozás], és tapasztalja meg, mire képes a szolgáltatás. 

Ezenfelül a [BizTalk felügyelt API-k][biztalk] révén a [szabálymotor][szabályok], a [kereskedelmipartner-kezelés][tpm] és még számos más funkció is rendelkezésére áll az összetett integrációs feladatok megoldására.

- **Könnyen használható tervezőeszközök** – A Logic Apps tervezése elejétől a végéig megvalósítható a böngészőben. Első lépés: az eseményindító – Ez lehet egy egyszerű ütemezés, de beállíthatja, hogy a folyamat akkor induljon el, amikor egy Twitter-üzenetben megemlítik a vállalatot. Ezt követően az összekötők széles választéka segítségével tetszőleges számú műveletet vezényelhet le.

- **Egyszerű SaaS-összeállítás** – Még az egyébként egyszerűen leírható összeállítási feladatokat is nehéz lehet kódban megírni. A Logic Apps szolgáltatásban pofonegyszerűen megoldható a különböző rendszerek összekötése. Szeretne létrehozni egy, a Facebook- vagy Twitter-fiókjában zajló tevékenységeken alapuló feladatot létrehozni CRM-szoftverében? Szeretné összekapcsolni felhőalapú marketingmegoldását a helyszíni számlázórendszerrel? A Logic Apps a lehető leggyorsabb és legmegbízhatóbb megoldást kínálja ezekre a feladatokra.

- **Gyors használatbavétel a sablonok révén** – Hogy megkönnyítsük a program használatának első lépéseit, összeállítottunk egy [sablongalériát][sablonok], amelynek alapján számos gyakran előforduló megoldás is rendkívül gyorsan megalkotható. Legyen szó akár BizTalk-megoldásokról, akár egyszerű SaaS-kapcsolatokról vagy akár a szórakozásról – a galéria segítségével tökéletes képet kaphat a Logic Apps hatékonyságáról.

- **Beépített bővíthetőség** – Nem találja az API-t, amit keres? A Logic Apps szolgáltatást úgy terveztük meg, hogy együttműködjön az API-alkalmazásokkal. Bárki könnyedén elkészítheti saját API-alkalmazását, amely egyéni API-ként használatba vehető. Hozza létre saját privát alkalmazását, vagy ossza meg és keressen pénzt művével a piactéren.

- **Komoly integrációs teljesítmény** – Kezdje könnyedén, és növekedjen az igényekkel együtt. A Logic Apps a BizTalk, a Microsoft piacvezető integrációs megoldásának erejével eszközöket ad az integrációs szakemberek kezébe, amelyekkel könnyedén kialakíthatják a saját igényeiknek leginkább megfelelő megoldásokat. További információk a [Logic Apps által biztosított BizTalk-funkciókról][biztalk].

## A logikai alkalmazások alapjai

Az alábbiakban a Logic Apps használatának kulcsfontosságú elemeit emeljük ki. 

- **Munkafolyamat** – A Logic Apps grafikus felületén lépések sorozataként vagy munkafolyamatként modellezheti az üzleti folyamatokat.
- **Felügyelt API-k** – A logikai alkalmazások adatokhoz és szolgáltatásokhoz való hozzáférést igényelnek. A felügyelt API-kat kifejezetten azért hoztuk létre, hogy segítséget nyújtsanak az adatokhoz való kapcsolódásban, és az azokkal való munkavégzésben. Tekintse meg, hogy milyen [felügyelt API-k érhetők el][felügyelt api-k].
- **Eseményindítók** – Egyes felügyelt API-k akár eseményindítóként is képesek működni. Az eseményindítóknak az a szerepük, hogy egy adott esemény bekövetkezésekor (például e-mail érkezésekor vagy az Azure-tárfiókban bekövetkezett változáskor) a munkafolyamat új példányát indítsák el.
-  **Műveletek** – Az eseményindítót követő lépéseket műveleteknek nevezzük. A műveletek általában összekapcsolódnak egy, a felügyelt vagy egyéni API-alkalmazásokban elvégzett tevékenységgel.
- **BizTalk** – Az összetettebb integrációs feladatokhoz a Logic Apps BizTalk-funkciókat tesz elérhetővé. A BizTalk a Microsoft piacvezető integrációs platformja. A BizTalk API-alkalmazások segítségével könnyedén vehet fel érvényesítést, átalakítást, szabályokat vagy más fontos műveletet a logikai alkalmazás munkafolyamatába. További információk: [a BizTalk API-alkalmazások bemutatása][biztalk].

## Első lépések  

 - Az első lépések megismeréséhez olvassa el a [Logikai alkalmazás létrehozása][létrehozás] című oktatóanyagot.  
 - [Gyakori példák és forgatókönyvek megtekintése](app-service-logic-examples-and-scenarios.md)
 - [Üzleti folyamatok automatizálása a Logic Apps segítségével](http://channel9.msdn.com/Events/Build/2016/T694) 
 - [A Logic Apps segítségével történő rendszerintegráció módjainak megismerése](http://channel9.msdn.com/Events/Build/2016/P462)
- További információk az Azure App Service platformról: [Azure App Service][appservice].

[biztalk]: app-service-logic-what-are-biztalk-api-apps.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[létrehozás]: app-service-logic-create-a-logic-app.md
[felügyelt api-k]: ../connectors/apis-list.md
[tpm]: app-service-logic-create-a-trading-partner-agreement.md
[szabályok]: app-service-logic-use-biztalk-rules.md
[sablonok]: app-service-logic-use-logic-app-templates.md



<!--HONumber=Jun16_HO2-->


