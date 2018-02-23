---
title: "Azure Naplóelemzés integrációs – gyakori kérdések |} Microsoft Docs"
description: "Ez a cikk az Azure napló integrációs kapcsolatos kérdésekre ad választ."
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
ms.date: 02/16/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 615bfb1ea86d31733fc1db7139cd995fbbbac7aa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-log-integration-faq"></a>Azure Naplóelemzés integrációs – gyakori kérdések

Ebben a cikkben megválaszolunk kapcsolatos gyakori kérdések (GYIK) Azure napló integráció.

>[!IMPORTANT]
>Az előnyben részesített módszere integrálása az Azure naplókat, hogy a SIEM gyártója által biztosított Azure figyelő összekötővel, és ezeket a következő [utasításokat](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Azonban ha a SIEM-szállító Azure figyelése nem biztosít egy összekötőt, akkor előfordulhat, hogy fogja tudni használni Azure napló integrációs ideiglenes megoldásként (Ha a SIEM Azure napló integrációs támogatja) addig, amíg az ilyen összekötő nem érhető el.

Azure napló-integráció egy olyan Windows operációs rendszer szolgáltatás, az Azure-erőforrások származó nyers naplók integrálja a helyszíni biztonsági információkat és az esemény (SIEM) felügyeleti rendszerek használható. Ez az integráció kínál az új, egységesített irányítópult minden Ez az eszköz, a helyszínen vagy a felhőben. Ezután összesíteni, összefüggéseket, elemzése, és az alkalmazások társított biztonsági események riasztást.

## <a name="is-the-azure-log-integration-software-free"></a>Az ingyenes Azure napló integrációs szoftver?
Igen. Nincs az Azure napló integrációs szoftver díjmentes.

## <a name="where-is-azure-log-integration-available"></a>Amennyiben Azure napló integrációs rendelkezésre áll?

Jelenleg az Azure kereskedelmi és Azure Government érhető el, és nem áll rendelkezésre a kínai vagy Németország.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Honnan látom, amelyből Azure napló integrációs Azure virtuális gép naplók van húzza a storage-fiókok?
Futtassa a parancsot **AzLog forráslista**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Hogyan állapítható meg az Azure napló integrációs naplók tartoznak, amelyhez az előfizetési?

Naplók kerülnek, amely esetén a **AzureResourcemanagerJson** könyvtárak, az előfizetés-azonosító szerepel a naplófájl nevét. Ugyanez a naplók a **AzureSecurityCenterJson** mappa. Példa:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Az Azure Active Directory-naplók a bérlő azonosítója a név részeként tartalmazza.

Az eseményközpontban lévő olvasható diagnosztikai naplói nem tartalmazzák az előfizetés-azonosító, a név részeként. Ehelyett a rövid nevét a hub eseményforrás létrehozása részeként megadott tartalmaznak. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hogyan frissíthetők a proxykonfigurációt?
Ha a megadott proxybeállítást nem teszi lehetővé az Azure storage access közvetlenül, nyissa meg a **AZLOG. EXE-FÁJL. CONFIG** fájlt **c:\Program Files\Microsoft Azure napló integrációs**. Frissítse a fájl elejéhez a **defaultProxy** a szervezet proxycímmel szakasza. A frissítés után állítsa le és indítsa el a szolgáltatást a parancsokkal **net stop AzLog** és **net start AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Honnan látom az előfizetési adatok a Windows-eseményeket?
Az előfizetés-azonosító hozzáfűzése a rövid nevet a forrás hozzáadása közben:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Az XML az esemény a következő metaadatokat, beleértve az előfizetés-azonosító van:

![Esemény XML][1]

## <a name="error-messages"></a>Hibaüzenetek
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>A parancs futtatásakor ```AzLog createazureid```, miért jelenik meg a következő hiba?
Hiba:

  *Nem sikerült létrehozni az alkalmazás AAD - 72f988bf-86f1-41af-91ab-2d7cd011db37-OK bérlői = "Tiltott" - Message = "Megfelelő jogosultsággal a művelet végrehajtása."*

A **azlog createazureid** parancs megpróbálja hozzon létre egy egyszerű szolgáltatást olyan előfizetést, amely az Azure bejelentkezési hozzáféréssel rendelkezik az Azure AD-bérlő. Ha az Azure bejelentkezési csak a Vendég felhasználó az Azure AD bérlőre, a parancs az "Megfelelő jogosultsággal a művelet végrehajtása." Kérje meg a fiók hozzáadása a bérlői felhasználói a bérlői rendszergazda.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>A parancs futtatásakor **azlog engedélyezik**, miért jelenik meg a következő hiba?
Hiba:

  *Szerepkör-hozzárendelés - AuthorizationFailed létrehozása figyelmeztetés: az ügyfél janedo@microsoft.com"objektum"fe9e03e4-4dad-4328-910f-fd24a9660bd2"azonosítója nem jogosult elvégezni művelet"Microsoft.Authorization/roleAssignments/write"hatókörben" / 97 – b971-0d8ff0000000 előfizetések / 70d 95299-d689-4c ".*

A **azlog engedélyezik** parancs olvasó szerepkört rendel hozzá az Azure AD szolgáltatás egyszerű (létre **azlog createazureid**) a megadott előfizetésekhez. Ha az Azure bejelentkezési nincs társadminisztrátorának vagy az előfizetés tulajdonosa, az nem "Engedélyezési sikertelen" hibaüzenet. Ez a művelet befejezéséhez szükséges Azure szerepköralapú hozzáférés-vezérlés (RBAC) társadminisztrátoraként vagy a tulajdonos.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Hol találok tulajdonságainak meghatározása a biztonsági naplóban?
Lásd:

* [Naplózási műveletek az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-audit.md)
* [A felügyeleti események a Azure REST API előfizetés felsorolása](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Hol található az Azure Security Center riasztásait részleteit?
Lásd: [kezelése és az Azure Security Centerben a biztonsági riasztásokra való reagálásról](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hogyan módosítható meg, hogy a Virtuálisgép-diagnosztika gyűjtött adatok?
Részletes információt lekérni, módosítására és az Azure diagnosztikai konfiguráció beállítása a következő témakörben: [Windows rendszerű virtuális gépként Azure Diagnostics engedélyezéséhez használja a Powershellt](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az alábbi példa lekérdezi az Azure diagnosztikai konfiguráció:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

A következő példa az Azure Diagnostics konfigurációját módosítja. Ebben a konfigurációban csak azonosító 4624 és esemény azonosítója 4625 gyűjtenek a biztonsági eseménynaplóba. A Microsoft Antimalware Azure események begyűjti a rendszer-eseménynaplójában. Az XPath kifejezések használatára, lásd: [fel események](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

A következő példa az Azure Diagnostics konfigurációjának beállítása:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

Módosítása után, ellenőrizze annak érdekében, hogy a megfelelő események gyűjtése történt a tárfiók.

Ha probléma merül fel a telepítés és konfigurálás során, nyisson meg egy [támogatási kérelem](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Válassza ki **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Azure napló integrációs használatával hálózati figyelőt naplók integrálja a saját SIEM?

Az Azure hálózati figyelőt naplózási információk nagy mennyiségű állít elő. Ezek a naplók nem jogosultak a SIEM küldendő. Hálózati figyelőt naplók csak támogatott célja egy tárfiókot. Azure napló-integráció nem támogatja a naplók beolvasásakor, és elérhetővé teszi azokat a siem-mel.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
