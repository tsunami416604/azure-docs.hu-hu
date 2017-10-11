---
title: "Az Operations Management Suite (OMS) integrálása |} Microsoft Docs"
description: "A szabványos OMS olyan szolgáltatását használja, mellett integrálva más felügyeleti alkalmazások és szolgáltatások egy hibrid környezetben, így a környezet egyedi egyéni felügyeleti lehetőségeket, vagy adjon meg egy egyéni felügyeleti az ügyfelek élmény.  Ez a cikk áttekintést OMS és részletes technikai információkat biztosító cikkek hivatkozásainak integrálása a különböző lehetőségek közül."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Az Operations Management Suite (OMS) integrálása
Az Operations Management Suite a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra.  A szabványos OMS olyan szolgáltatását használja, mellett integrálva más felügyeleti alkalmazások és szolgáltatások egy hibrid környezetben, így a környezet egyedi egyéni felügyeleti lehetőségeket, vagy adjon meg egy egyéni felügyeleti az ügyfelek élmény.  Ez a cikk áttekintést OMS-szolgáltatások és részletes technikai információkat biztosító cikkek hivatkozásainak integrálása a különböző lehetőségek közül. 

## <a name="log-analytics"></a>Log Analytics
A tárházban lévő Azure tárolódjanak a webhelykezelési adatok Naplóelemzési által gyűjtött.  A tárházban tárolt összes adat áll rendelkezésre, amelyek rendkívül nagy adatmennyiségek gyors elemzés biztosít napló keresi.  Az integráció követelményeit lehet feltölti a tárház új adatokkal, és az elemzés elérhetővé válik, vagy az új képi megjelenítés vagy egy másik felügyeleti eszköz integrálása a tárházban lévő adatok kinyerése érdekében.

Minden adat a tárházban bejegyzésként tárolja.  A tárház tölthető fel, amikor a felhasználók kell biztosítania a a megoldást használó rekord típus és a tulajdonságok leírása.  Visszaállíthatja az adatokat, ha tájékoztatásra van szüksége a dolgozunk adatokról.

![Az OMS-tárház feltöltése](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>A Naplóelemzési tárház feltöltése
Több módon a az OMS-tárház feltöltéséhez.  A használt módszer függ a tényezőket, mint ahol a forrásadatok, az adatokat, és amely formátuma ügyfelek támogatásához szükséges.  Ha a tárház adatokat tárolja, nincs különbség, hogy gyűjtötte teszi.

A következő szakaszok ismertetik a különböző lehetőségek közül az OMS-tárház feltöltéséhez.

#### <a name="connected-sources-and-data-sources"></a>Csatlakoztatott adatforrások és adatforrások
Csatlakoztatott adatforrások olyan a helyek, ahol lehet adatokat beolvasni az OMS-tárház.  Adatforrások és a megoldás a csatlakoztatott források futtatásához, és állítsa be az összegyűjtött adatokat.  Ha az alkalmazás adatokat ír az adatforrások közül, majd gyűjtheti, úgy konfigurálja az adatforrást.  Például, ha az alkalmazás hozza létre a Syslog-események, majd azok gyűjthetők a Syslog-adatforrás a Linux-ügynök.

* [A Naplóelemzési adatforrások](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Megoldások
Megoldások OMS képességekkel bővíthetik.  A megoldás lehet, hogy adatokat gyűjteni a csatlakoztatott adatforrás, vagy az analysis hajthat végre a rekordokat a tárházban már gyűjtött.  Egyes Microsoft által biztosított megoldások, amely az adatokat az adatokon, amely összegyűjti az egyes cikk tartalmaz.

* [A Naplóelemzési megoldások](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP adatgyűjtő API
A napló Analytics HTTP Data Collector API a REST API-t, amely lehetővé teszi a JSON-adatokat hozzáadni a Naplóelemzési tárház.  Ez az API használhatja, ha egy alkalmazás, amely nem biztosítja az adatok egy más adatforrások vagy megoldások keresztül.  A tárház bármely ügyfél meghívhatja az API-t, és nem szükséges minden adatforrás vagy a megoldás a adatgyűjtési ütemezést feltöltéséhez használható.

* [Elemzés HTTP adatok naplógyűjtő API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>A Naplóelemzési tárház adatainak lekérése
Több módon az adatok beolvasása az OMS-tárházat.  Előfordulhat, hogy szeretné beolvasni az adatokat az OMS-konzol használatával a felhasználók, és adja meg különböző képi megjelenítések és elemzése.  Például egy másik felügyeleti megoldás külső folyamat is lekérik az adatokat.

#### <a name="log-searches"></a>Napló-keresések
Az OMS-tárházban tárolt összes adat napló keresések keresztül érhető el.  Felhasználók előfordulhat, hogy a saját alkalmi elemzést az OMS-konzolon, vagy hozzon létre egy irányítópultot a képi megjelenítés, adott napló keresés.  Megoldások tartalmazhatnak egyéni nézetek alapján előre definiált keresések megjelenítésekkel.  A naplófájl-keresési API használatával férnek hozzá a adatokhoz az OMS-tárházban, a külső alkalmazás- vagy felügyeleti eszköz.  

* [A Naplóelemzési napló-keresések](../log-analytics/log-analytics-log-searches.md)
* [A Naplóelemzési jelentkezzen search REST API-n](../log-analytics/log-analytics-log-search-api.md)
* [Napló Analytics parancsmagok](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Egyéni nézetek
Az adatforrásnézet-tervezőből egyéni nézetek létrehozása az OMS konzolon láthatja, hogy módszereket biztosítsanak a felhasználóknak a képi megjelenítés és a megoldás az adatok elemzését teszi lehetővé.  Mindegyik nézetről tartalmaz egy csempe bármely Ön által meghatározott napló keresések alapuló képi megjelenítés-részek száma és a konzol fő lapján látható.

* [Napló Analytics adatforrásnézet-tervezőből](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
A Naplóelemzési automatikusan adatait exportálhatja az OMS-tárház Power BI-ba így kihasználhatja a képi megjelenítések és elemzésére szolgáló eszközöket.  Elvégez ehhez az exporthoz ütemezés szerint, az adatok van mindig naprakészek legyenek. 

* [A Naplóelemzési adatok exportálása a Power bi-bA](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automatizálás
OMS automatizálhatja az összegyűjtött adatok reagálni vagy más felügyeleti funkciók folyamatokat.  Azt előfordulhat, hogy az alkalmazás adatokat gyűjteni, és helyezze be az OMS-tárház, vagy előfordulhat, hogy automatizálja a tárházban található adatok válaszul egy ismert probléma javítása. 

![Automatizálás](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbookok
Az Azure Automation Runbookjai az Azure felhőben futtatható PowerShell-parancsfájlok és munkafolyamatok.  Erőforrások az Azure-ban vagy a felhőből elérhető más erőforrások kezeléséhez használhatja őket.  Runbookok használata a hibrid forgatókönyv-feldolgozó helyi adatközpontban is futtatható.  Egy runbook megkezdheti az Azure portál vagy külső folyamatok, számos módszer, például a PowerShell vagy az Automation API használatával.

* [Runbook elindítása az Azure Automationben](../automation/automation-starting-a-runbook.md)
* [Azure Automation parancsmagokkal](https://msdn.microsoft.com/library/dn690262.aspx)
* [Automatizálási REST API-n](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automatizálási .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Riasztások
A riasztási szabályok automatikusan napló megkeresi a megadott ütemezés szerint futtatni.  Ha az eredmények adott feltételeknek megfelelő az eredményül kapott riasztás elindít egy forgatókönyvet az Azure Automation, vagy hívja a webhook, amely elindíthatja egy külső folyamatban.  Mindkét ezeket a válaszokat lehetnek többek között a naplófájl-keresési szereplő adatok a riasztás részleteit.

* [A Naplóelemzési riasztások](../log-analytics/log-analytics-alerts.md)
* [Napló Analytics riasztási API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Biztonsági mentés és helyreállítás
Az Azure biztonsági mentés és helyreállítás a vállalati adatok védelme és a kiszolgálók és alkalmazások rendelkezésre állásának biztosítása a szolgáltatásokhoz.  Ezek a szolgáltatások ilyen forgatókönyvek például az alkalmazás biztonsági mentési szolgáltatások vagy a virtuális gépek a feladatátvétel kezdeményezése végrehajtásához használhatja.

* [Az Azure biztonsági mentést készítő parancsmagok](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST API-n](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Az Azure Site Recovery-parancsmagok](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Egyéni megoldások
A munkaterület vagy az ügyfél munkaterületen egyéni megoldásba történő is foglalják magukban integrációs programot.  A megoldás tartalmazhat bármilyen, az integráció módszerek mellett egyéb erőforrásai adjon meg egy teljes körű forgatókönyv ebben a cikkben.  A megoldás az erőforrások vannak csomagolva, úgy, hogy a megoldás eltávolítása után a létrehozott erőforrások el lesznek távolítva az OMS-munkaterület és az Azure-előfizetés.

Például a megoldás tartalmazhatnak egy Automation-runbook gyűjtsön és feldolgozni az adatokat, és töltse fel tagokkal a Naplóelemzési tárházat, a HTTP-adatokat gyűjtő API használatával.  Egyéni nézet, amely megadja, és elemzi az összegyűjtött adatokat is tartalmazhatnak.  

* Egyéni megoldások (hamarosan elérhető)    

## <a name="next-steps"></a>Következő lépések
* Hivatkozás a [OMS SDK](operations-management-suite-sdk.md) technikai információkat az OMS-szolgáltatások automatizálása.  

