---
title: Azure Automation-adatok kezelése
description: Ez a cikk több témakört tartalmaz egy Azure Automation környezet kezeléséhez.  Jelenleg az adatok megőrzését és a Azure Automation Azure Automation vész-helyreállítás biztonsági mentését foglalja magában.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da1b151a150dfbf602593451d3d68043352b73eb
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850771"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-adatok kezelése
Ez a cikk több témakört tartalmaz egy Azure Automation környezet kezeléséhez.

## <a name="data-retention"></a>Adatmegőrzés
Amikor Azure Automationban töröl egy erőforrást, a rendszer a naplózási célokra 90 napig őrzi meg a végleges eltávolítást.  Ebben az időszakban nem tekintheti meg és nem használhatja az erőforrást.  Ez a szabályzat a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik.

Azure Automation automatikusan törli és véglegesen eltávolítja a 90 napnál régebbi feladatokat.

A következő táblázat összefoglalja a különböző erőforrások adatmegőrzési szabályzatát.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Véglegesen eltávolított 90 nappal azután, hogy egy felhasználó törölte a fiókot. |
| Objektumok |Véglegesen eltávolított 90 nappal azután, hogy az eszközt egy felhasználó törölte, vagy 90 nappal azután, hogy egy felhasználó törölte az adategységet. |
| Modulok |Véglegesen eltávolított 90 nappal azután, hogy egy felhasználó törölte a modult, vagy 90 nappal azután, hogy egy felhasználó törölte a modult. |
| Runbookok |Véglegesen eltávolított 90 nappal azután, hogy egy felhasználó törölte az erőforrást, vagy 90 nappal azután, hogy egy felhasználó törölte az erőforrást tartalmazó fiókot. |
| Feladatok |Törölve és véglegesen eltávolítva 90 nappal az utolsó módosítás után. Ez a feladatok befejeződése után leáll, vagy fel van függesztve. |
| Csomópont-konfigurációk/MOF-fájlok |A régi csomópont-konfigurációt a rendszer véglegesen eltávolítja 90 nappal azután, hogy új csomópont-konfiguráció jön létre. |
| DSC-csomópontok |A rendszer véglegesen eltávolította 90 nappal azt követően, hogy a csomópontot nem regisztrálta az Automation-fiókból Azure Portal vagy a [Regisztráció törlése-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) parancsmag használatával a Windows PowerShellben. A csomópontokat a rendszer véglegesen eltávolítja 90 nappal azután is, hogy egy felhasználó törölte azt a fiókot, amely a csomópontot tárolja. |
| Csomópont-jelentések |Véglegesen eltávolított 90 nappal azután, hogy új jelentés jön létre az adott csomóponthoz |

Az adatmegőrzési szabályzat minden felhasználóra érvényes, és jelenleg nem szabható testre.

Ha azonban hosszabb ideig kell megőriznie az adatok mennyiségét, a runbook-feladatok naplóit továbbíthatja Azure Monitor naplókba.  További információkért tekintse át [Azure Automation feladatának továbbítása Azure monitor naplókhoz](automation-manage-send-joblogs-log-analytics.md)című témakört.   

## <a name="backing-up-azure-automation"></a>Az Azure Automation biztonsági mentése
Ha Microsoft Azure-ben töröl egy Automation-fiókot, a rendszer törli a fiókban lévő összes objektumot, beleértve a runbookok, a modulokat, a konfigurációkat, a beállításokat, a feladatokat és az eszközöket. A fiók törlése után az objektumok nem állíthatók helyre.  A következő információk segítségével biztonsági másolatot készíthet az Automation-fiók tartalmáról a törlés előtt. 

### <a name="runbooks"></a>Runbookok
A runbookok a Windows PowerShell Azure Portal vagy a [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) parancsmagjának használatával exportálhatja parancsfájl-fájlokba.  Ezeket a parancsfájlokat egy másik Automation-fiókba importálhatja, amelyet a [Runbook létrehozása vagy importálása](/previous-versions/azure/dn643637(v=azure.100))című cikkben talál.

### <a name="integration-modules"></a>Integrációs modulok
Azure Automationból nem exportálhatja az integrációs modulokat.  Gondoskodnia kell arról, hogy az Automation-fiókon kívül is elérhetők legyenek.

### <a name="assets"></a>Objektumok
Nem lehet exportálni az [eszközöket](/previous-versions/azure/dn939988(v=azure.100)) a Azure Automationból.  A Azure Portal használatával meg kell jegyeznie a változók, a hitelesítő adatok, a tanúsítványok, a kapcsolatok és az ütemtervek részleteit.  Ezután manuálisan kell létrehoznia azokat az eszközöket, amelyeket egy másik automatizálásba importált runbookok használ.

Az Azure- [parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) segítségével lekérheti a nem titkosított eszközök részleteit, és megtakaríthatja őket későbbi használatra, vagy egy másik Automation-fiókban egyenértékű eszközöket hozhat létre.

A titkosított változók vagy a hitelesítő adatok jelszava mezője nem kérhető le a parancsmagok használatával.  Ha nem ismeri ezeket az értékeket, a [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) és a [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) tevékenységek segítségével lekérheti őket egy runbook.

A tanúsítványok nem exportálhatók Azure Automationból.  Győződjön meg arról, hogy az Azure-on kívül minden tanúsítvány elérhető.

### <a name="dsc-configurations"></a>DSC-konfigurációk
A konfigurációkat a Windows PowerShell Azure Portal vagy [export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) parancsmagjának használatával is exportálhatja parancsfájl-fájlokba. Ezeket a konfigurációkat egy másik Automation-fiókban is importálhatja és használhatja.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikálás Azure Automation
A Geo-replikáció, a Azure Automation-fiókok standard tagja, biztonsági mentést készít a fiókadatok számára egy másik földrajzi régióhoz a redundancia érdekében. A fiók beállításakor kiválaszthat egy elsődleges régiót, majd a rendszer automatikusan hozzárendeli a másodlagos régiót. Az elsődleges régióból másolt másodlagos adatok adatvesztés esetén folyamatosan frissülnek.  

A következő táblázat a rendelkezésre álló elsődleges és másodlagos régió párosításait mutatja be.

| Elsődleges | Másodlagos |
| --- | --- |
| USA déli középső régiója |USA északi középső régiója |
| USA 2. keleti régiója |USA középső régiója |
| Nyugat-Európa |Észak-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |
| Kelet-Japán |Nyugat-Japán |

Abban az esetben, ha az elsődleges régió adatainak elvesztése nem valószínű, a Microsoft megkísérli a helyreállítást. Ha az elsődleges adatait nem lehet helyreállítani, a rendszer a Geo-feladatátvételt hajtja végre, és az érintett ügyfeleket az előfizetésen keresztül kapja meg.


