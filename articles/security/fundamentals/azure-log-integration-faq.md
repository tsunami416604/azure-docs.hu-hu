---
title: Azure Log Integration GYIK | Microsoft Docs
description: Ez a cikk a Azure Log Integrationával kapcsolatos kérdésekre ad választ.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7ed63364a9dc4b96470a23af3a4bff832d42621b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727611"
---
# <a name="azure-log-integration-faq"></a>Azure Log Integration GYIK

Ez a cikk a Azure Log Integration kapcsolatos gyakori kérdésekre (GYIK) válaszol.

>[!IMPORTANT]
> Az Azure log-integrációs szolgáltatást a 06/15/2019-as rendszer elavulttá teszi. A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

A Azure Log Integration egy olyan Windows operációs rendszerbeli szolgáltatás, amellyel a nyers naplókat integrálhatja az Azure-erőforrásokból a helyszíni biztonsági információk és az Event Management (SIEM) rendszerbe. Ez az integráció egy egységesített irányítópultot biztosít a helyszíni vagy a felhőben található összes eszközhöz. Ezután összesítheti, korrelálhatja, elemezheti és riasztást készíthet az alkalmazásaihoz társított biztonsági eseményekről.

Az Azure-naplók integrálásának előnyben részesített módszere az SIEM-gyártó Azure Monitor-összekötő használata, és a következő [utasítások](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)követése. Ha azonban az SIEM-szállító nem biztosít összekötőt a Azure Monitorhoz, előfordulhat, hogy a Azure Log Integrationt ideiglenes megoldásként használhatja (ha a SIEM-et a Azure Log Integration támogatja), amíg az összekötő elérhetővé válik.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Ingyenes a Azure Log Integration szoftver?

Igen. A Azure Log Integration szoftverért nem számítunk fel díjat.

## <a name="where-is-azure-log-integration-available"></a>Hol érhető el Azure Log Integration?

Jelenleg az Azure kereskedelmi és Azure Government részeként érhető el, és nem érhető el Kínában és Németországban.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Hogyan tekinthetem meg a Storage-fiókokat, amelyekből Azure Log Integration az Azure-beli virtuális gépek naplói?

Futtassa a **AzLog**parancsot.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Honnan tudhatom meg, hogy milyen előfizetéshez tartoznak a Azure Log Integration naplók?

A **AzureResourcemanagerJson** -címtárakba helyezett naplók esetében az előfizetés azonosítója a naplófájl nevében található. Ez a **AzureSecurityCenterJson** mappában található naplók esetében is igaz. Példa:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory naplózási naplók tartalmazzák a bérlő AZONOSÍTÓját a név részeként.

Az Event hub-ból beolvasott diagnosztikai naplók nem tartalmazzák az előfizetés AZONOSÍTÓját a név részeként. Ehelyett az Event hub-forrás létrehozásának részeként megadott rövid nevet kell tartalmazniuk. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hogyan frissíthetem a proxy konfigurációját?

Ha a proxy beállítása nem engedélyezi közvetlenül az Azure Storage-hozzáférést, nyissa meg a **AZLOG. EXE. KONFIGURÁCIÓS** fájl a **c:\Program Files\Microsoft Azure log Integration**. Frissítse a fájlt, hogy tartalmazza a **defaultProxy** szakaszt a szervezete proxyjának címeként. A frissítés befejezése után állítsa le és indítsa el a szolgáltatást a **net stop AzLog** és a **net start AzLog**parancs használatával.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hogyan tekinthetem meg az előfizetési adatokat a Windows-események között?

Fűzze hozzá az előfizetés-azonosítót a felhasználóbarát névhez a forrás hozzáadásakor:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Az Event XML a következő metaadatokat tartalmazza, beleértve az előfizetés-azonosítót:

![Esemény XML-fájlja](media/azure-log-integration-faq/event-xml.png)

## <a name="error-messages"></a>Hibaüzenetek
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>A parancs ```AzLog createazureid```futtatásakor Miért kapok a következő hibaüzenetet?

Hiba:

  *Nem sikerült létrehozni a HRE alkalmazást – bérlő 72f988bf-86f1-41af-91ab-2d7cd011db37-Reason = ' tiltott '-Message = ' nincs megfelelő jogosultsága a művelet végrehajtásához. "*

A **azlog createazureid** parancs megkísérli létrehozni az összes Azure ad-bérlőben az Azure-bejelentkezéshez hozzáférő előfizetésekhez tartozó egyszerű szolgáltatásnevet. Ha az Azure-beli bejelentkezés csak egy vendég felhasználó az Azure AD-bérlőben, a parancs "nem megfelelő jogosultsággal rendelkezik a művelet végrehajtásához". Kérje meg a bérlői rendszergazdát, hogy vegye fel a fiókot felhasználóként a bérlőbe.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>A **azlog**parancs futtatásakor a következő hibaüzenet jelenik meg:

Hiba:

  *Figyelmeztetés a szerepkör-hozzárendelés létrehozásához – AuthorizationFailed: A (z\@) fe9e03e4-4dad-4328-910f-fd24a9660bd2 azonosítójú Microsoft.com ügyfél janedo nem rendelkezik a (z) "Microsoft. Authorization/roleAssignments/Write" művelet végrehajtásához szükséges engedéllyel a hatókör "/Subscriptions/ 70d95299-d689-4c97-b971-0d8ff0000000'.*

A **azlog engedélyezése** parancs az olvasó szerepkörét az Azure ad egyszerű szolgáltatásához rendeli hozzá (létrehozva a **azlog createazureid**) a megadott előfizetésekhez. Ha az Azure-beli bejelentkezés nem rendszergazda vagy az előfizetés tulajdonosa, az "engedélyezés sikertelen" hibaüzenet jelenik meg. A művelet végrehajtásához Azure szerepköralapú Access Control (RBAC) szükséges a társ-rendszergazda vagy a tulajdonos számára.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Hol találhatom meg a tulajdonságok definícióját a naplóban?

Lásd:

* [Auditálási műveletek Azure Resource Manager](/azure/azure-resource-manager/resource-group-audit)
* [Egy előfizetésben szereplő felügyeleti események listázása a Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Hol találhatók részletek a Azure Security Center-riasztásokról?

Lásd: a [biztonsági riasztások kezelése és válaszadás a Azure Security Center](/azure/security-center/security-center-managing-and-responding-alerts).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hogyan módosíthatom a virtuálisgép-diagnosztika használatával gyűjtött adatokat?

A Azure Diagnostics konfigurációjának beszerzésével, módosításával és beállításával kapcsolatos további információkért lásd: a [Azure Diagnostics engedélyezése a PowerShell használatával a Windows rendszerű virtuális gépeken](/azure/virtual-machines/windows/ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

A következő példa a Azure Diagnostics konfigurációját kéri le:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

A következő példa a Azure Diagnostics konfigurációját módosítja. Ebben a konfigurációban csak a 4624-es azonosítójú eseményazonosító és a 4625-es azonosítójú esemény kerül begyűjtésre a biztonsági eseménynaplóból. A rendszer az eseménynaplóból gyűjti össze a Microsoft antimalware for Azure-eseményeket. Az XPath-kifejezések használatával kapcsolatos részletekért lásd: az [események](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85))felhasználása.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

A következő példa a Azure Diagnostics konfigurációját állítja be:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

A módosítások elvégzése után ellenőrizze a Storage-fiókot, és győződjön meg arról, hogy a megfelelő események gyűjtése megtörtént.

Ha a telepítés és a konfigurálás során problémák merülnek fel, nyisson meg egy [támogatási kérést](/azure/azure-supportability/how-to-create-azure-support-request). Válassza ki a **naplózási integráció** lehetőséget arra a szolgáltatásra, amelyhez támogatást kér.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Használhatom a Azure Log Integration Network Watcher naplók integrálásához a SIEM-be?

Az Azure Network Watcher nagy mennyiségű naplózási információt generál. Ezeket a naplókat nem kell a SIEM-nek elküldeni. A Network Watcher naplók egyetlen támogatott célpontja egy Storage-fiók. Azure Log Integration nem támogatja ezeket a naplókat, és elérhetővé teszi őket egy SIEM-hez.


