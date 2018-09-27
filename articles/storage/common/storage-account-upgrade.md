---
title: Frissítés az általános célú v2 tárfiók – Azure Storage |} A Microsoft Docs
description: Általános célú v2-tárfiókok frissítse.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 6e77c4836531a7efd0b52b9a411ac40ff6a613fa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224494"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Egy általános célú v2-tárfiók frissítése

Általános célú v2-tárfiókok a legújabb Azure Storage-szolgáltatások támogatásához, és építse be az összes funkcióját, általános célú v1 és Blob storage-fiókok. Általános célú v2 fiókok használata akkor javasolt, a storage legtöbb forgatókönyvhöz. Általános célú v2 fiókok kapacitás árak nyújthat a legalacsonyabb gigabájtonkénti az Azure Storage, valamint iparági versenyképes tranzakciós.

Frissítés az általános célú v1, általános célú v2 tárfiók vagy a Blob storage-fiókok használata egyszerű. Az Azure portal, PowerShell vagy az Azure CLI használatával is frissítheti. Fiók frissítése nem vonható vissza, és a számlázási költségeket vonhat.

## <a name="upgrade-using-the-azure-portal"></a>Frissítse az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. Az a **beállítások** területén kattintson **konfigurációs**.
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét. 
6. Kattintson a **frissítése** a panel alján.

## <a name="upgrade-with-powershell"></a>Frissítés a PowerShell-lel

Egy általános célú v2 fiókját a PowerShell segítségével egy általános célú v1 fiók frissítéséhez először frissítse PowerShell legújabb verzióját használja a **AzureRm.Storage** modul. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket. 

Ezután hívja meg a fiókot, és cserélje le az erőforráscsoport nevét és a storage-fiók frissítése a következő parancsot:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Frissítés az Azure CLI-vel

Egy általános célú v2 fiók az Azure CLI-vel egy általános célú v1 fiók frissíteni, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető szakaszt. 

Ezután hívja meg a fiókot, és cserélje le az erőforráscsoport nevét és a storage-fiók frissítése a következő parancsot:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Blobadatok a hozzáférési szint megadását

Általános célú v2 támogatja az összes Azure storage-szolgáltatások és adatok objektum, de az elérési szint csak a Blob storage-ban a blokkblobokhoz érhető el. Amikor frissít egy általános célú v2-tárfiók, megadhatja a hozzáférési szint a blob adatait. 

Hozzáférés csomagjai lehetővé teszik, hogy válassza ki a leginkább költséghatékony tárolási megoldás a várható használati mintái alapján. A blokkblobok tárolhatók a gyakori és ritka elérésű vagy archív szint. A hozzáférési rétegek további információkért lásd: [Azure Blob storage: gyakori, ritka és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

Alapértelmezés szerint egy új storage-fiók jön létre a gyakran használt adatok tároláselérési rétegében és a egy általános célú v1 storage-fiók frissítése a gyakori elérésű hozzáférési szintre. Ha, a vizsgált milyen hozzáférési szint használata az adatok frissítése, fontolja meg a forgatókönyvnek. Egy általános célú v2 fiók való áttelepítés során két jellemző felhasználói forgatókönyv közül választhat:

* Rendelkezik egy meglévő általános célú v1-tárfiókot, és szeretné kiértékelni egy általános célú v2 tárfiók blobadatokat a megfelelő tárolási szintjére való áttérést.
* Úgy döntött, használjon egy általános célú v2-tárfiók vagy már rendelkezik ilyennel, és szeretné meghatározni, hogy blobadatokat kell használni a gyors vagy lassú elérésű tárolási szinten.

Mindkét esetben az első érték tárolása, elérése és a egy általános célú v2-tárfiók-ban tárolt adatokkal kapcsolatos működtetése költségének és összehasonlítása az aktuális költségekkel.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Az aktuális felhasználási mintáin költségeinek

Tárolási költségek becsléséhez és a egy általános célú v2-tárfiók az egy adott szinten lévő Blobadatok eléréséhez értékelnie a jelenlegi használati módot vagy a várható használati módot hozzávetőleges. Általában a következőket érdemes figyelembe venni:

* A Blob storage fogyasztásánál, gigabájtban, többek között:
    - Mennyi adatot tárol a tárfiókjában?
    - Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?
* Az elsődleges elérési mintája a Blob storage-adatokból, többek között:
    - Folyamatban van, hogy mennyi adatot olvas és ír a storage-fiókhoz? 
    - Hány olvasási műveletek és írási műveleteket a rendszer az adatok a storage-fiókban?

Az igényeinek megfelelően a legjobb hozzáférési szint használata mellett dönt, hogy hasznos lehet meghatározni, mekkora kapacitást a Blobadatok jelenleg használ, és hogyan adatokat használják. 

A tárfiók a migrálás előtt a használati adatok gyűjtéséhez, nyomon követheti a tárfiók tárfiókkulcsait [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Az Azure Monitor naplózhatja és mérőszámadatokat biztosít az Azure-szolgáltatásokhoz, mint az Azure Storage. 

A tárfiókban található blobok felhasználási adatok monitorozásához engedélyezése az Azure monitorban a kapacitási mérőszámot. A kapacitás metrikák a fiókjában található blobokat használ, naponta mennyi tárhelyre vonatkozó adatokat rögzíti. Kapacitás mérőszámok segítségével megbecsülheti költségeit az adatok tárolása a storage-fiók. Hogyan Blob storage kapacitási díjszabása az egyes fiókkal kapcsolatban lásd: [Block blob díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/).

A Blob Storage adathozzáférési mintáinak figyeléséhez, engedélyezze az Azure monitorban tranzakció-mérőszámot. A megbecsülni, hogy milyen gyakran nevezik minden más Azure Storage műveleteket végezhet. Megtudhatja, hogyan különböző típusú tranzakciók díjszabása vonatkozóan, és a hozzáfűző blobok minden fióktípushoz tartozó, lásd: [Block blob díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Az Azure Monitor metrikákat gyűjt kapcsolatos további információkért lásd: [Azure Storage-mérőszámok az Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](storage-quickstart-create-account.md)
- [Az Azure storage-fiókok kezelése](storage-account-manage.md)