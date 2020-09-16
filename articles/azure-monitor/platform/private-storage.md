---
title: Ügyfél által felügyelt Storage-fiókok használata Azure Monitor Log Analytics
description: Saját Storage-fiók használata Log Analytics forgatókönyvekhez
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 9d54e6eb84e3269eb95f8d314875474f78536652
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526425"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Ügyfél által felügyelt Storage-fiókok használata Azure Monitor Log Analytics

A Log Analytics különböző forgatókönyvekben támaszkodik az Azure Storage-ra. Ezt a használatot általában automatikusan kezeli a rendszer. Bizonyos esetekben azonban a saját Storage-fiók (más néven ügyfél által felügyelt Storage-fiók) megadására és felügyeletére van szükség. Ez a dokumentum részletesen ismerteti az ügyfél által felügyelt tárolók használatát a WAD/LAD naplók betöltéséhez, a privát hivatkozások adott forgatókönyvekhez és a CMK-titkosításhoz. 

> [!NOTE]
> Azt javasoljuk, hogy ne használja függőséget az ügyfél által felügyelt tárhelyre Log Analytics feltöltéseket, mivel a formázás és a tartalom változhat.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics-bővítményi naplók betöltése (WAD/LAD)
A Azure Diagnostics Extension Agents (más néven WAD és LAD Windows-és Linux-ügynökök esetében) különböző operációsrendszer-naplókat gyűjt, és egy ügyfél által felügyelt Storage-fiókba tárolja őket. Ezután betöltheti ezeket a naplókat Log Analyticsba, hogy áttekintse és elemezze őket.
Azure Diagnostics-bővítményi naplók összegyűjtése a Storage-fiókból a Storage-fiók és a Log Analytics munkaterület tárolási adatforrásként való összekapcsolásával [a Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) vagy a Storage-ELEMZÉSi [API](https://docs.microsoft.com/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate)meghívásával.

Támogatott adattípusok:
* Rendszernapló
* Windows-események
* Service Fabric
* ETW események
* IIS-naplók

## <a name="using-private-links"></a>Privát hivatkozások használata
Az ügyfelek által felügyelt tárolási fiókok bizonyos használati esetekben szükségesek, ha Azure Monitor erőforrásokhoz való kapcsolódásra használják a magánhálózati hivatkozásokat. Az egyik ilyen eset az egyéni naplók vagy az IIS-naplók betöltése. Ezek az adattípusok először blobként lesznek feltöltve egy közvetítő Azure Storage-fiókba, és csak ezután kerülnek be a munkaterületre. Hasonlóképpen, egyes Azure Monitor megoldások a Storage-fiókokat is használhatják a Azure Security Center megoldás által használt nagyméretű fájlok, például a Watson-memóriaképek tárolására. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Ügyfél által felügyelt tárolót igénylő privát hivatkozásokra vonatkozó forgatókönyvek
* Egyéni naplók és IIS-naplók betöltése
* Az ASC-megoldás engedélyezése a Watson-memóriaképek gyűjtéséhez

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Ügyfél által felügyelt Storage-fiók használata privát kapcsolaton keresztül
##### <a name="workspace-requirements"></a>Munkaterület-követelmények
Amikor privát kapcsolaton keresztül csatlakozik Azure Monitorhoz, Log Analytics-ügynökök csak privát kapcsolaton keresztül küldhetnek naplókat a hálózathoz kapcsolódó munkaterületekre. Ehhez a szabályhoz megfelelően konfigurálnia kell egy Azure Monitor Private link scope (AMPLS) objektumot, csatlakoztatni kell a munkaterületekhez, majd csatlakoztatnia kell a AMPLS a hálózatához privát kapcsolaton keresztül. A AMPLS konfigurációs eljárással kapcsolatos további információkért lásd: az [Azure privát hivatkozás használata a hálózatok Azure monitorhoz való biztonságos csatlakoztatásához](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security). 
##### <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények
Ahhoz, hogy a Storage-fiók sikeresen csatlakozhasson a privát kapcsolathoz, a következőket kell tennie:
* A VNet vagy egy összekapcsolt hálózaton található, és privát kapcsolaton keresztül csatlakozik a VNet. Ez lehetővé teszi a VNet lévő ügynökök számára a naplók küldését a Storage-fiókba.
* Ugyanazon a régión kell lennie, mint a munkaterületnek, amelyhez kapcsolódik.
* A Storage-fiók elérésének engedélyezése Azure Monitor számára. Ha úgy döntött, hogy csak a hálózatok kiválasztását engedélyezi a Storage-fiókhoz, akkor ezt a kivételt is engedélyeznie kell: "a megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a Storage-fiókhoz". Ez lehetővé teszi Log Analytics számára, hogy beolvassa a Storage-fiókba betöltött naplókat.
* Ha a munkaterület más hálózatokból is kezeli a forgalmat, akkor konfigurálnia kell a Storage-fiókot, hogy engedélyezze a kapcsolódó hálózatokból/internetről érkező bejövő forgalmat.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>A Storage-fiók összekapcsolása egy Log Analytics munkaterülettel
A Storage-fiók a munkaterülethez az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) -n vagy a [Rest APIon](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts)keresztül kapcsolható. Alkalmazható dataSourceType-értékek:
* CustomLogs – az egyéni naplók és az IIS-naplók tárterületének használata a betöltés során.
* AzureWatson – használja az ASC (Azure Security Center) megoldás által feltöltött Watson-memóriaképek tárolását. További információ az adatmegőrzés kezeléséről, a társított Storage-fiók lecseréléséről és a Storage-fiók tevékenységének figyeléséről: a [társított Storage-fiókok kezelése](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Az adattitkosítás a CMK
Az Azure Storage-ban a Storage-fiókokban tárolt összes adatok titkosítva vannak. Alapértelmezés szerint a Microsoft által felügyelt kulcsokkal (MMK) titkosítja az adataikat. Az Azure Storage azonban lehetővé teszi az ügyfél által felügyelt kulcs (CMK) használatát az Azure Key vaultból a tárolási adatok titkosításához. Importálhatja a saját kulcsait Azure Key Vaultba, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-forgatókönyvek, amelyekhez ügyfél által felügyelt Storage-fiók szükséges
* Napló-riasztási lekérdezések titkosítása a CMK
* Mentett lekérdezések titkosítása a CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>A CMK alkalmazása az ügyfél által felügyelt Storage-fiókokra
##### <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények
A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. Az Azure Storage titkosításával és a kulcskezelő szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

##### <a name="apply-cmk-to-your-storage-accounts"></a>CMK alkalmazása a Storage-fiókokra
Ha úgy szeretné konfigurálni az Azure Storage-fiókot, hogy az ügyfél által felügyelt kulcsokat Azure Key Vault használja, használja a [Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal?toc=/azure/storage/blobs/toc.json), a [PowerShellt](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-powershell?toc=/azure/storage/blobs/toc.json) vagy a [parancssori](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-cli?toc=/azure/storage/blobs/toc.json)felületet. 

## <a name="managing-linked-storage-accounts"></a>Társított Storage-fiókok kezelése

A Storage-fiókok munkaterülethez való csatolásához vagy leválasztásához használja az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) -t vagy a [REST API](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Hivatkozás létrehozása vagy módosítása
Ha egy munkaterülethez csatolja a Storage-fiókot, Log Analytics a szolgáltatás által birtokolt Storage-fiók helyett elkezdi használni. képes vagy 
* Több Storage-fiók regisztrálása a naplók közötti terhelés elosztásához
* Ugyanazt a Storage-fiókot használja több munkaterülethez

##### <a name="unlink-a-storage-account"></a>Storage-fiók csatolásának megszüntetése
A Storage-fiók használatának megszüntetéséhez kapcsolja ki a tárolót a munkaterületről. Az összes Storage-fiók egy munkaterületről való leválasztása azt jelenti, Log Analytics megkísérli a szolgáltatás által felügyelt Storage-fiókokra támaszkodni. Ha a hálózat korlátozottan fér hozzá az internethez, előfordulhat, hogy ezek a tárolók nem érhetők el, és a tárterületre támaszkodó forgatókönyvek sikertelenek lesznek.

##### <a name="replace-a-storage-account"></a>Storage-fiók cseréje
A betöltéshez használt Storage-fiók cseréje
1.  **Hozzon létre egy új Storage-fiókra mutató hivatkozást.** A naplózási ügynökök megkapják a frissített konfigurációt, és megkezdik az adatok küldését az új tárhelyre is. A folyamat eltarthat néhány percig.
2.  **Ezután leválaszthatja a régi Storage-fiókot, hogy az ügynökök ne kelljen írni az eltávolított fiókba.** A betöltési folyamat megtartja az adatoknak a fiókból való beolvasását mindaddig, amíg az összes adatot be nem tölti. Addig ne törölje a Storage-fiókot, amíg meg nem jelenik az összes napló betöltése.

### <a name="maintaining-storage-accounts"></a>Storage-fiókok karbantartása
##### <a name="manage-log-retention"></a>Napló megőrzésének kezelése
Saját Storage-fiók használata esetén a megőrzési idő az Ön számára. Más szóval Log Analytics nem törli a privát tárhelyen tárolt naplókat. Ehelyett úgy kell beállítania a házirendet, hogy a beállításoknak megfelelően kezelje a terhelést.

##### <a name="consider-load"></a>Vegye figyelembe a betöltést
A Storage-fiókok bizonyos terhelésű olvasási és írási kérelmeket képesek kezelni a kérelmek szabályozásának megkezdése előtt (további részletekért lásd a [blob Storage skálázhatósági és teljesítményi céljait](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account) ismertető témakört). A szabályozás a naplók betöltéséhez szükséges időt befolyásolja. Ha a Storage-fiók túl van terhelve, regisztráljon egy további Storage-fiókot a terhelés elosztásához. A Storage-fiók kapacitásának és teljesítményének figyeléséhez tekintse át [a Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Kapcsolódó díjak
A Storage-fiókokat a tárolt adatmennyiség, a tároló típusa és a redundancia típusa alapján számítjuk fel. Részletekért lásd: a [Blobok díjszabásának](https://azure.microsoft.com/pricing/details/storage/blobs) és a [Table Storage díjszabásának](https://azure.microsoft.com/pricing/details/storage/tables)blokkolása.


## <a name="next-steps"></a>További lépések

- Tudnivalók az [Azure Private-hivatkozás használatáról a hálózatok Azure monitorhoz való biztonságos csatlakoztatásához](private-link-security.md)
- Ismerkedjen meg [Azure monitor ügyfél által felügyelt kulcsokkal](customer-managed-keys.md)
