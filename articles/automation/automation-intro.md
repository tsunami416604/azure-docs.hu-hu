---
title: "Az Azure Automation bemutatása | Microsoft Docs"
description: "Megtudhatja, mit kínál az Azure Automation, és választ kaphat gyakori kérdésekre, hogy nekiláthasson a forgatókönyvek Azure Automation DSC-ben való létrehozásának és használatának."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
keywords: "az Automation ismertetése, Azure Automation, Azure Automation példák"
ms.assetid: 0cf1f3e8-dd30-4f33-b52a-e148e97802a9
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2016
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 6b1fb9e7ae810df21cbcd592fef2b43309e2269c
ms.contentlocale: hu-hu
ms.lasthandoff: 07/10/2017

---
# <a name="azure-automation-overview"></a>Az Azure Automation áttekintése
A Microsoft Azure Automation segítségével a felhasználók automatizálhatják a manuális, hosszan tartó, sok hibalehetőséget rejtő és gyakran ismétlődő feladatokat, amelyeket gyakran hajtanak végre a felhőben és vállalati környezetben. Ezzel idő takarítható meg, továbbá nő a rendszeres adminisztratív feladatok megbízhatósága, valamint még ütemezhetők is a feladatok, hogy adott időközönként automatikusan végrehajtsa őket a rendszer. A folyamatokat automatizálhatja forgatókönyvek segítségével, vagy automatizálhat konfigurációkezelést a Célállapot-konfigurációval (DSC). Ez a cikk röviden áttekinti az Azure Automationt, valamint választ ad néhány gyakran felmerülő kérdésre. A különböző témakörök részletesebb leírásáért tekintse meg a könyvtár egyéb cikkeit.

## <a name="automating-processes-with-runbooks"></a>Folyamatok automatizálása forgatókönyvekkel
A forgatókönyv egy feladatkészlet, amely bizonyos automatizált folyamatokat hajt végre az Azure Automationben. Ez lehet egy egyszerű folyamat, mint például egy virtuális gép elindítása vagy egy naplóbejegyzés létrehozása, illetve a forgatókönyv lehet komplex, amely más, kisebb forgatókönyveket összekapcsolva több erőforrásban, vagy akár több felhőben és helyszíni környezetben dolgozik.  

Ha például van egy meglévő manuális eljárása egy SQL-adatbázis csonkolására abban az esetben, ha elérte a maximális méretet, és ez az eljárás több lépést tartalmaz, például egy kiszolgálóhoz való csatlakozást, az adatbázishoz való csatlakozást, az adatbázis aktuális méretének lekérését, valamint a küszöbérték elérésének ellenőrzését, majd a csonkolást és a felhasználó értesítését. Ahelyett, hogy ezeket a lépéseket manuálisan hajtaná végre, létrehozhat egy forgatókönyvet, amely egyetlen folyamatban hajtja végre az összes feladatot. A forgatókönyv elindításakor meg kell adnia a szükséges információkat, például az SQL-kiszolgáló nevét, az adatbázis nevét és a címzett e-mail-címét, és onnantól hátradőlve figyelheti, ahogy a folyamat lezajlik. 

## <a name="what-can-runbooks-automate"></a>Mit lehet automatizálni forgatókönyvekkel?
Az Azure Automation forgatókönyvek Windows PowerShellen vagy Windows PowerShell-munkafolyamaton alapulnak, tehát képesek mindenre, amire a PowerShell. Ha egy alkalmazás vagy szolgáltatás rendelkezik API-val, a forgatókönyv tudja azt használni. Ha van PowerShell-modulja az alkalmazáshoz, akkor betöltheti a modult az Azure Automationbe, és szerepeltetheti annak a parancsmagjait a fogatókönyvében. Az Azure Automation forgatókönyvek az Azure-felhőben futnak, és bármilyen felhőalapú erőforráshoz vagy külső erőforráshoz hozzáférnek, amely elérhető a felhőből. Egy [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md) segítségével a forgatókönyvek futtathatók a helyi adatközpontban, és kezelhetők velük helyi erőforrások. 

## <a name="getting-runbooks-from-the-community"></a>Forgatókönyvek beszerzése a közösségtől
A [Forgatókönyv galéria](automation-runbook-gallery.md#runbooks-in-runbook-gallery) a Microsoft és a közösség forgatókönyveit tartalmazza. Ezeket felhasználhatja módosítás nélkül a környezetében, vagy testreszabhatja őket, hogy megfeleljenek a céljainak. Referenciaként is hasznosak annak megtanulásához, hogy miként hozhatja létre a saját forgatókönyveit. A saját forgatókönyveit is hozzáadhatja a galériához, ha más felhasználók számára is hasznosnak gondolja. 

## <a name="creating-runbooks-with-azure-automation"></a>Forgatókönyvek létrehozása Azure Automationnel
[Létrehozhatja a saját forgatókönyveit](automation-creating-importing-runbook.md) az alapoktól felépítve, vagy módosíthat a [Forgatókönyv galériából](http://msdn.microsoft.com/library/azure/dn781422.aspx) szerzett forgatókönyveket a saját igényeinek megfelelően. Négy különböző [forgatókönyvtípus](automation-runbook-types.md) közül választhat az igényei és a PowerShell-tapasztalata alapján. Ha közvetlenül a PowerShell-kóddal szeretne dolgozni, offline vagy az Azure portál [szöveges szerkesztőjével](http://msdn.microsoft.com/library/azure/dn879137.aspx) szerkesztheti a [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks) vagy a [PowerShell-alapú munkafolyamat-forgatókönyvet](automation-runbook-types.md#powershell-workflow-runbooks). Ha inkább a kód közvetlen manipulálása nélkül szeretne szerkeszteni egy forgatókönyvet, létrehozhat egy [grafikus forgatókönyvet](automation-runbook-types.md#graphical-runbooks) az Azure portál [grafikus szerkesztőjével](automation-graphical-authoring-intro.md). 

Inkább néz videót olvasás helyett? Tekintse meg az alábbi videót a Microsoft Ignite 2015. májusi üléséről. Megjegyzés: Noha a videóban tárgyalt koncepciók és szolgáltatások helyesek, az Azure Automation sokat fejlődött a videó rögzítése óta. Mostanra kiterjedtebb a felhasználói felülete az Azure portálon, és további képességeket is biztosít.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3451/player]
> 
> 

## <a name="automating-configuration-management-with-desired-state-configuration"></a>A konfigurációkezelés automatizálása a célállapot-konfigurációval (DSC)
A [PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) egy felügyeleti platform, amely lehetővé teszi fizikai gazdagépek és virtuális gépek konfigurációjának kezelését, telepítését és kényszerítését egy deklaratív PowerShell-szintaxis segítségével. Meghatározhat a célgépeken automatikusan lekérhető és alkalmazható konfigurációkat egy központi DSC lekéréses kiszolgálón. A DSC biztosít egy PowerShell-parancsmagkészletet, amely segítségével automatikusan kezelheti a konfigurációkat és erőforrásokat.  

Az [Azure Automation DSC](automation-dsc-overview.md) egy felhőalapú megoldás a PowerShell DSC-hez, amely vállalati környezetekhez szükséges szolgáltatásokat biztosít.  A DSC-erőforrásait kezelheti az Azure Automationben, és alkalmazhat konfigurációkat virtuális és fizikai gépekre, amelyek azokat az Azure-felhőben lévő DSC lekéréses kiszolgálóról szerzik be.  Jelentéskészítési szolgáltatásokat is biztosít, amelyek tájékoztatást adnak olyan fontos eseményekről, mint a csomók letérése a kijelölt konfigurációról vagy egy új konfiguráció alkalmazása 

## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Saját DSC-konfigurációk létrehozása az Azure Automationnel
A [DSC-konfigurációk](automation-dsc-overview.md) meghatározzák a csomópont kívánt állapotát.  Több csomópont alkalmazhatja ugyanazt a konfigurációt annak biztosítására, hogy mind azonos állapotot tartsanak fenn.  Létrehozhat egy konfigurációt bármilyen szövegszerkesztővel a helyi gépén, majd importálhatja az Azure Automationbe, ahol lefordíthatja, majd alkalmazhatja a csomópontokra.

## <a name="getting-modules-and-configurations"></a>Modulok és konfigurációk beszerzése
A forgatókönyveiben és a DSC-konfigurációiban használható parancsmagokat tartalmazó [PowerShell-modulokat](automation-runbook-gallery.md#modules-in-powershell-gallery) beszerezhet a [PowerShell-galériából](http://www.powershellgallery.com/). Ezt a galériát az Azure portálról indíthatja el, a modulokat pedig importálhatja közvetlenül az Azure Automationbe, vagy letöltheti őket, és importálhatja manuálisan. A modulokat nem telepítheti közvetlenül az Azure portálról, de letöltheti, majd telepítheti őket, mint bármely egyéb modult. 

## <a name="example-practical-applications-of-azure-automation"></a>Az Azure Automation gyakorlati alkalmazásai
Az alábbiakban csupán néhány példát láthat arra, hogy milyen automatizálási forgatókönyvek képzelhetők el az Azure Automation használatával. 

* Különböző Azure-előfizetésekben lévő virtuális gépek létrehozása és másolása. 
* Fájlmásolások ütemezése egy helyi gépről egy Azure Blob Storage tárolóba. 
* Biztonsági funkciók automatizálása (pl. egy ügyfél kéréseinek megtagadása, amikor a rendszer szolgáltatásmegtagadási támadást észlel). 
* Annak biztosítása, hogy a gépek folyamatosan igazodnak a konfigurált biztonsági házirendhez.
* Az alkalmazáskód folyamatos telepítése a felhőben és a helyszíni infrastruktúrán. 
* Active Directory-erdő létrehozása az Azure-ban a tesztkörnyezethez. 
* Tábla csonkolása egy SQL-adatbázisban, ha az adatbázis már majdnem elérte a maximális méretet. 
* A környezeti beállítások távoli frissítése egy Azure-webhelyhez. 

## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Hogyan kapcsolódik az Azure Automation más automatizálási eszközökhöz?
A [Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) célja a felügyeleti feladatok automatizálása a privát felhőben. Helyben van telepítve az adatközpontban a [Microsoft Azure csomag](https://www.microsoft.com/en-us/server-cloud/) részeként. Az SMA és az Azure Automation ugyanazt a Windows PowerShellen és Windows PowerShell-munkafolyamaton alapuló forgatókönyv-formátumot használja, azonban az SMA nem támogatja a [grafikus forgatókönyveket](automation-graphical-authoring-intro.md).  

A [System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) célja a helyszíni erőforrások automatizálása. Más forgatókönyv-formátumot használ, mint az Azure Automation és a Service Management Automation, valamint a grafikus felület segítségével parancsfájlkezelés nélkül lehet létrehozni forgatókönyveket. A forgatókönyvei kifejezetten az Orchestrator számára írt integrációs csomag tevékenységeiből állnak össze. 

## <a name="where-can-i-get-more-information"></a>Hol kaphatok további információkat?
Számos erőforrás áll rendelkezésére, hogy további információkat kapjon az Azure Automationről és a saját forgatókönyvei létrehozásáról. 

* Az **Azure Automation Library** a hely, ahol jelenleg van. A könyvtár cikkei átfogó dokumentációt biztosítanak az Azure Automation konfigurálásával és adminisztrációjával, valamint a saját forgatókönyvei létrehozásával kapcsolatban. 
* Az [Azure PowerShell-parancsmagok](http://msdn.microsoft.com/library/jj156055.aspx) az Azure műveletek Windows PowerShell segítségével való automatizálásáról nyújtanak információkat. A forgatókönyvek ezen parancsmagok segítségével használják az Azure-erőforrásokat. 
* A [Felügyeleti blog](https://azure.microsoft.com/blog/tag/azure-automation/) a legújabb információt biztosítja az Azure Automation és a Microsoft egyéb felügyeleti technológiáinak vonatkozásában. Erre a blogra előfizetve mindig naprakészen követheti az Azure Automation csapatának legfrissebb híreit. 
* Az [Automation fórum](http://go.microsoft.com/fwlink/p/?LinkId=390561) lehetővé teszi, hogy kérdéseket tegyen fel az Azure Automationről, és választ kapjon a Microsofttól vagy az Automation közösségtől. 
* Az [Azure Automation parancsmagok](https://msdn.microsoft.com/library/mt244122.aspx) az adminisztrációs feladatok automatizálásához nyújtanak információt. Parancsmagokat tartalmaz az Automation-fiókok, adategységek, forgatókönyvek és a DSC kezeléséhez.

## <a name="can-i-provide-feedback"></a>Küldhetek visszajelzést?
**Várjuk a visszajelzését!** Ha egy Azure Automation-forgatókönyv megoldást vagy egy integrációs modult keres, küldjön egy Parancsfájlkérelmet a Script Centerbe. Ha visszajelzést vagy funkciókérést küldene az Azure Automation vonatkozásában, tegye azt közzé a [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) fórumon. Köszönjük! 


