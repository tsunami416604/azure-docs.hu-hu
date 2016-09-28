<properties
 pageTitle="Mi az Azure Scheduler? | Microsoft Azure"
 description="Az Azure Scheduler lehetőséget biztosít a felhőben futtatandó műveletek deklaratív leírására. A rendszer ezt követően a műveletek ütemezését és futtatását automatikusan végzi el."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>


# Mi az Azure Scheduler?

Az Azure Scheduler lehetőséget biztosít a felhőben futtatandó műveletek deklaratív leírására. A rendszer ezt követően a műveletek ütemezését és futtatását automatikusan végzi el.  A Scheduler ezt [az Azure portál](scheduler-get-started-portal.md), a kód, [a REST API](https://msdn.microsoft.com/library/mt629143.aspx) vagy az Azure PowerShell használatával végzi el.

A Scheduler elvégzi az ütemezett munkák létrehozását, karbantartását és meghívását.  A Scheduler nem futtat számítási feladatokat vagy kódokat. A szolgáltatás csupán _meghívja_ a máshol (az Azure-ban, helyszínen vagy másik szolgáltató által) futtatott kódokat. A meghívás a következőkön keresztül történhet: HTTP, HTTPS, tárolási sor, Service Bus-üzenetsor vagy Service Bus-témakör.

A Scheduler elvégzi a [feladatok](scheduler-concepts-terms.md) ütemezését, áttekinthető formában rögzíti a feladat-végrehajtási előzményeket, illetve determinisztikus és megbízható módon ütemezi a futtatandó számítási feladatokat. Az Azure WebJobs (az Azure App Service Web Apps szolgáltatásának része) és az Azure további ütemezési szolgáltatásai a Schedulert a háttérben futtatják. A [Scheduler REST API](https://msdn.microsoft.com/library/mt629143.aspx) megkönnyíti a műveletekkel való kommunikációt. Ezért a Scheduler támogatja a [komplex és speciális, ismétlődő ütemezések](scheduler-advanced-complexity.md) egyszerű létrehozását.

A Scheduler számos különböző helyzetben alkalmazható kiválóan. Példa:

+ _Ismétlődő alkalmazásműveletek:_ A Twitteren megjelenő adatok hírcsatornába foglalása.
+ _Napi karbantartás_: A naplók naponta történő törlése, biztonsági másolatok készítése és egyéb karbantartási feladatok. A rendszergazda például megadhatja, hogy hajnali 1 órakor kíván az adatbázisról biztonsági mentést készíteni a következő kilenc hónap minden napján.

A Scheduler segítségével szoftveresen hozhat létre, frissíthet, törölhet, tekinthet meg és felügyelhet feladatokat és [feladatgyűjteményeket](scheduler-concepts-terms.md), parancsprogramok használatával vagy a portálon.

## Lásd még:

 [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure portálon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Komplex és speciális, ismétlődő ütemezések létrehozása az Azure Scheduler használatával](scheduler-advanced-complexity.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

 [Azure Scheduler – kimeneti hitelesítés](scheduler-outbound-authentication.md)



<!--HONumber=Sep16_HO4-->


