---
title: Az Azure Log Integration – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk az Azure Log Integration kapcsolatos kérdésekre ad választ.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 06/07/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 458b615ea872b62a412a71cbbb0b3827ccb4d9b8
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580926"
---
# <a name="azure-log-integration-faq"></a>Az Azure Log Integration – gyakori kérdések

Ebben a cikkben megválaszolunk Azure Log Integration kapcsolatos gyakori kérdések (GYIK).

>[!IMPORTANT]
> Az Azure Log integration szolgáltatás 06/01/2019 elavulttá válik. AzLog letöltések 2018. június 27. letiltásra kerül. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Az Azure Log Integration szolgáltatása egy Windows operációs rendszer, amellyel az Azure-erőforrások nyers naplóinak integrálása a helyszíni biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti rendszerek. Ez az integráció az egységesített irányítópult kínál az összes eszközt, a helyszíni vagy a felhőben. Ezután összesíteni, összekapcsolását, elemzése, és az alkalmazásokkal kapcsolatos biztonsági események a riasztást.

Az előnyben részesített módja az Azure-naplók integrálása a SIEM szállítói s Azure Monitor-összekötő használatával, és ezek a következő van [utasításokat](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Azonban a SIEM szállítói adatforgalmi-t az Azure monitornak biztosítani egy összekötőt, ha, előfordulhat, hogy tudni használni az Azure Log Integration ideiglenes megoldás (Ha a siem-nek az Azure Log Integration) mindaddig, amíg elérhető ilyen egy összekötőt.

## <a name="is-the-azure-log-integration-software-free"></a>Az Azure Log Integration szoftver ingyenes?

Igen. Nem jár költséggel az Azure Log Integration szoftverek.

## <a name="where-is-azure-log-integration-available"></a>Hol érhető el az Azure Log Integration?

Az Azure kereskedelmi és az Azure Government jelenleg elérhető és a nem áll rendelkezésre a China vagy Germany szolgáltatásba.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Hogyan tekinthetem meg a storage-fiókok, amelyről az Azure Log Integration lehetőség van az Azure virtuális gépek naplóinak?

Futtassa a parancsot **AzLog forráslista**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Honnan tudom, hogy melyik előfizetést az Azure Log Integration naplók származnak?

Auditnaplók kerülnek, amely esetén a **AzureResourcemanagerJson** könyvtárak, az előfizetés-azonosító szerepel a naplófájl nevét. Ez akkor is igaz, a naplók a **AzureSecurityCenterJson** mappát. Példa:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Az Azure Active Directory-auditnaplók a bérlő Azonosítóját, a név részeként tartalmazza.

Diagnosztikai naplók egy adott eseményközpontból olvasható nem tartalmazza az előfizetés-Azonosítót a nevének részeként. Ehelyett a hub-eseményforrás létrehozása részeként megadott rövid nevét tartalmazzák. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hogyan frissíthetem a proxykiszolgáló-konfigurációt?

Ha a proxy beállításai nem engedélyezi a hozzáférést az Azure storage közvetlenül, nyissa meg a **AZLOG. EXE-FÁJL. CONFIG** fájlt **c:\Program Files\Microsoft Azure Log Integration**. A fájl frissítése a **defaultProxy** a szervezet proxycímmel szakaszában. Miután a frissítés elkészült, állítsa le és indítsa el a szolgáltatást a parancsokkal **net stop AzLog** és **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hogyan tekinthetem meg az előfizetési információkat a Windows-eseményeket?

Az előfizetés-azonosító hozzáfűzése a valódi nevet a forrás hozzáadása közben:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Az esemény XML rendelkezik a következő metaadatokat, beleértve az előfizetés-azonosító:

![Esemény XML][1]

## <a name="error-messages"></a>Hibaüzenetek
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>A parancs futtatásakor ```AzLog createazureid```, miért jelenik meg a következő hiba?

Hiba:

  *Nem sikerült létrehozni az AAD-alkalmazás - 72f988bf-86f1-41af-91ab-2d7cd011db37-OK bérlői = "Tiltott" – üzenet = "Nincs megfelelő jogosultsága a művelet végrehajtásához."*

A **azlog createazureid** a parancs megpróbál egy egyszerű szolgáltatás létrehozása az előfizetéseket, amely hozzáféréssel rendelkezik az Azure-azonosító az Azure AD-bérlőt. Ha az Azure bejelentkezési csak egy adott Azure AD-bérlőjében vendégfelhasználó, a parancs az "Nincs megfelelő jogosultsága a művelet végrehajtásához." Kérje meg a bérlői rendszergazda a bérlő felhasználói fiók hozzáadásához.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>A parancs futtatásakor **azlog engedélyezése**, miért jelenik meg a következő hiba?

Hiba:

  *Szerepkör-hozzárendelés - AuthorizationFailed létrehozása figyelmeztetés: Az ügyfél janedo@microsoft.com"objektum"fe9e03e4-4dad-4328-910f-fd24a9660bd2"azonosítója nem rendelkezik a 'Microsoft.Authorization/roleAssignments/write' művelet végrehajtására"/ subscriptions/70 95299-d689-4 c d 97 – b971-0d8ff0000000"hatókörben.*

A **azlog engedélyezése** parancsot az Olvasó szerepkörhöz rendel az Azure AD egyszerű szolgáltatás (létrehozott **azlog createazureid**) a megadott előfizetéshez. Ha az Azure-azonosító nem egy társ-rendszergazdaként vagy az előfizetés tulajdonosa, "Engedélyezése nem sikerült" hibaüzenettel meghiúsul. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) társ-rendszergazdaként vagy a tulajdonos van szükség a művelet végrehajtásához.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Hol találhatok tulajdonságainak meghatározása a naplóban?

Lásd:

* [Auditálási műveletek az Azure Resource Managerrel](../azure-resource-manager/resource-group-audit.md)
* [Az Azure Monitor REST API-ban egy adott előfizetés felügyeleti eseményeinek listázása](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Hol található az Azure Security Center riasztásait a részletei?

Lásd: [kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hogyan módosítható meg, hogy a Virtuálisgép-diagnosztika gyűjtött adatok?

Megtudhatja, hogyan szeretne kapni, módosíthatja, és állítsa be az Azure Diagnostics konfigurációja, lásd: [Windows rendszerű virtuális gép az Azure Diagnostics engedélyezéséhez használja a Powershellt](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az alábbi példa lekéri az Azure Diagnostics konfigurálása:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Az alábbi példa módosítja az Azure Diagnostics konfigurációja. Ebben a konfigurációban csak event ID 4624 és azonosító 4625-ös számú esemény összegyűjtése az a biztonsági eseménynaplóba. A Microsoft Antimalware az Azure-eseményeket a rendszer eseménynaplójába gyűjtenek. XPath-kifejezéseket használatára vonatkozó részletekért lásd: [események felhasználásához](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Az alábbi példa az Azure Diagnostics konfigurációjának beállítása:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Miután változtatásokat, ellenőrizze a storage-fiók segítségével, győződjön meg arról, hogy a megfelelő események gyűjtött.

Ha problémája van a telepítés és konfigurálás során, nyisson egy [támogatási kérelem](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Válassza ki **Naplóintegráció** a kért támogatási szolgáltatással.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Azure Log Integration segítségével integrálhatók a Network Watcher naplók saját siem-be?

Az Azure Network Watcher állít elő, nagy mennyiségű naplózási információk. Ezek a naplók nem jelentenek a kell küldeni a siem-be. A Network Watcher naplók az egyetlen támogatott cél tárfiók. Az Azure Log Integration nem támogatja a naplók olvasása és elérhetővé teszi azokat a SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
