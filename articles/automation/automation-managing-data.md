---
title: Azure Automation-adatok kezelése
description: Ez a cikk egy Azure Automation környezet kezeléséhez több témaköröket tartalmazza.  Jelenleg magában foglalja az adatok megőrzésére és biztonsági mentése az Azure Automation-vészhelyreállítás az Azure Automationben.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b133947a7daf1977f084ee4008599882b51d9e8b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428477"
---
# <a name="managing-azure-automation-data"></a>Azure Automation-adatok kezelése
Ez a cikk egy Azure Automation környezet kezeléséhez több témaköröket tartalmazza.

## <a name="data-retention"></a>Adatmegőrzés
Ha töröl egy erőforrást az Azure Automationben, véglegesen eltávolításuk előtt naplózási célokra 90 napig őrződnek meg.  Nem lásd: vagy az erőforrás használatára ebben az időszakban.  Ez a szabályzat vonatkozik tartozó automation-fiók törlődött az erőforrásokhoz is.

Az Azure Automation automatikusan törli, és véglegesen eltávolítja a 90 napnál régebbi feladatok.

Az alábbi táblázat foglalja össze a különböző erőforrások esetén a megtartási házirendben.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Véglegesen törli a felhasználó által a fiók törlése után 90 napig. |
| Objektumok |Véglegesen törli az eszköz felhasználó által törlése után 90 napig, vagy 90 nappal követően a fiók társításához, hogy az eszköz törlődik a felhasználó által. |
| Modulok |Véglegesen törli a felhasználó által a modul törlése után 90 napig, vagy 90 nappal követően a fiók társításához, hogy a modul törlődött a felhasználó által. |
| Runbookok |Véglegesen törli a felhasználó által az erőforrás törlése után 90 napig, vagy 90 nappal követően a fiókot, amely tárolja a felhasználó által az erőforrás törléséig. |
| Feladatok |Törölt, és véglegesen eltávolított 90 nappal követően az utolsó módosítás alatt. Ennek oka az lehet, miután a feladat befejeződik, le van állítva vagy fel van függesztve. |
| Csomópont-konfigurációk/MOF-fájlok |Régi csomópont-konfiguráció követő 90 napon belül jön létre egy új csomópont-konfiguráció véglegesen törlődnek. |
| DSC-csomópontok |Véglegesen törli a 90 nap után a szolgáltatásért Automation-fiók az Azure portal használatával, vagy a [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) parancsmagot a Windows PowerShellben. Csomópontok is véglegesen eltávolítja a fiókot, amely tartalmazza a csomópont nem törli azokat a felhasználó után 90 napig. |
| Csomópont-jelentések |Véglegesen törli az adott csomópont egy új jelentéskészítés után 90 nappal |

Az adatmegőrzési házirend minden felhasználóra vonatkozik, és jelenleg nem lehet testreszabni.

Azonban ha szeretne egy hosszabb ideig megőrizni az adatokat, is továbbíthatja, runbook feladatnaplóit a Log Analytics szolgáltatásba.  További információkért tekintse át a [Azure Automation-feladat adatainak továbbítása a Log Analytics](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Az Azure Automation biztonsági mentése
Ha töröl egy automation-fiókot a Microsoft Azure-ban, a rendszer törli a fiókban lévő összes objektumra, többek között a runbookok, modulok, konfigurációk, beállítások, feladatokat és eszközöket. Az objektumok nem állítható helyre, miután a fiókot törölték.  Az alábbi információkat használhatja a biztonsági mentés az automation-fiók tartalmát azok törlése előtt. 

### <a name="runbooks"></a>Runbookok
A runbookok exportálhatók. a parancsfájlok vagy az Azure portal használatával, vagy a [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) parancsmagot a Windows PowerShellben.  Ezek a parancsfájlok importálható egy másik automation-fiókba az [létrehozása vagy importálása a Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Integrációs modulok
Az Azure Automation integrációs modulok nem exportálhatja.  Biztosítania kell, hogy azok elérhetők kívül az automation-fiókot.

### <a name="assets"></a>Objektumok
Nem lehet exportálni [eszközök](https://msdn.microsoft.com/library/dn939988.aspx) az Azure Automationben.  Az Azure portal használatával kell vegye figyelembe a változókat, hitelesítő adatok, tanúsítványok, kapcsolatok és ütemezések részleteit.  Ezután manuálisan kell létrehoznia egy másik automation alkalmazásba importált runbookok által használt összes olyan adategység.

Használhat [Azure parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) részleteit nem titkosított eszközökre, és menti őket a későbbiekben beolvasni, vagy azzal egyenértékű objektumok létrehozása egy másik automation-fiókban.

Az érték a titkosított változók vagy a jelszó mező parancsmagokkal hitelesítő adatok nem olvashatók be.  Ha nem ismeri ezeket az értékeket, majd a runbookok kérheti a [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) és [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) tevékenységeket.

Tanúsítványok nem exportálhatja az Azure Automationben.  Biztosítania kell, hogy a tanúsítványok érhetők el Azure-on kívül.

### <a name="dsc-configurations"></a>DSC-konfigurációk
A konfigurációk exportálhatók. a parancsfájlok vagy az Azure portal használatával, vagy a [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) parancsmagot a Windows PowerShellben. Ezek a konfigurációk importálható és használt egy másik automation-fiókban.

## <a name="geo-replication-in-azure-automation"></a>Georeplikáció az Azure Automationben
Georeplikáció, az Azure Automation-fiókok, standard menti a redundancia érdekében egy másik földrajzi régióban fiókja adatait. Választhat egy elsődleges régióban, a fiók beállítása során, és ezután egy másodlagos régióba rendel hozzá automatikusan. Adatvesztés esetén a másodlagos, az elsődleges régió másolt adatokat folyamatosan frissítjük.  

Az alábbi táblázat a rendelkezésre álló elsődleges és másodlagos régiónkénti párosításról.

| Elsődleges | Másodlagos |
| --- | --- |
| USA déli középső régiója |USA északi középső régiója |
| USA 2. keleti régiója |USA középső régiója |
| Nyugat-Európa |Észak-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |
| Kelet-Japán |Nyugat-Japán |

Az, hogy egy elsődleges régióban adat elvész nem túl valószínű esetben Microsoft próbál helyreállítani. Ha az elsődleges adatok nem állíthatók be, majd a földrajzi feladatátvétel megy végbe, és az érintett ügyfelek értesítjük erről az előfizetés keretében.


