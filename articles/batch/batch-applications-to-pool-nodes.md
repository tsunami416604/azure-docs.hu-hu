---
title: Alkalmazások és az alkalmazások másolása a készlet csomópontjaira
description: Ismerje meg, hogyan másolhat alkalmazásokat és információkat a készlet csomópontjaira.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954893"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Alkalmazások és az alkalmazások másolása a készlet csomópontjaira

Azure Batch számos módszert támogat az adatok és alkalmazások számítási csomópontokra történő beolvasására, hogy az adatok és alkalmazások elérhetők legyenek a feladatok számára. Előfordulhat, hogy az adatok és alkalmazások futtatásához a teljes feladatot futtatni kell, ezért minden csomóponton telepítve kell lennie. Néhány esetben csak egy adott feladathoz lehet szükséges, vagy telepíteni kell a feladathoz, de nem kell minden csomóponton lennie. A Batch eszközöket tartalmaz az egyes forgatókönyvekhez.

- **Készlet indítási tevékenységének erőforrás-fájljai**: olyan alkalmazásokhoz vagy adatokhoz, amelyeket a készlet minden csomópontján telepíteni kell. A telepítési parancs végrehajtásához használja ezt a metódust vagy egy alkalmazáscsomag vagy az indítási tevékenység erőforrásfájl-gyűjteményét.  

Példák: 
- Alkalmazások áthelyezése vagy telepítése a feladat indítása parancssor használatával

- Adott fájlok vagy tárolók listájának megadásához egy Azure Storage-fiókban. További információ: [Add # resourcefile in Rest dokumentáció](/rest/api/batchservice/pool/add#resourcefile)

- A készleten futó összes feladattípus MyApplication.exe fut, amelyet először MyApplication.msi kell telepíteni. Ha ezt a mechanizmust használja, be kell állítania az indítási tevékenység **várakozási** idejének sikerességét **true**értékre. További információ: [# startTask hozzáadása a REST dokumentációban](/rest/api/batchservice/pool/add#starttask).

- **Alkalmazáscsomag-hivatkozások** a készletre: olyan alkalmazásokhoz vagy adatokhoz, amelyeket a készlet minden csomópontján telepíteni kell. Nincs hozzárendelve telepítési parancs egy alkalmazáscsomag számára, de az indítási tevékenységgel bármilyen telepítési parancsot futtathat. Ha az alkalmazás nem igényel telepítést, vagy nagy mennyiségű fájlt tartalmaz, ezt a metódust használhatja. Az alkalmazáscsomag nagy mennyiségű fájlhoz alkalmas, mert nagy mennyiségű fájl hivatkozást egyesít egy kis adattartalommal. Ha több mint 100 különálló erőforrást próbál felvenni egyetlen feladatba, előfordulhat, hogy a Batch szolgáltatás egy adott feladat belső rendszerkorlátain kívül esik. Az alkalmazás-csomagokat is használhatja, ha olyan szigorú verziószámozási követelményekkel rendelkezik, amelyekben számos különböző verziója lehet ugyanazon alkalmazásnak, és ezek közül kell választania. További információért olvassa el az [alkalmazások üzembe helyezése számítási csomópontokhoz kötegelt alkalmazáscsomag használatával](./batch-application-packages.md)című témakört.

- **Feladat-előkészítési tevékenység erőforrás-fájljai**: olyan alkalmazásokhoz vagy adatokhoz, amelyeket telepíteni kell a feladat futtatásához, de nem kell a teljes készletre telepíteni. Ha például a készlet számos különböző típusú feladattal rendelkezik, és csak egy feladattípusra van szükség MyApplication.msi futtatásához, érdemes a telepítési lépést a feladat-előkészítési feladatba helyezni. A feladat-előkészítési feladatokkal kapcsolatos további információkért lásd: [feladat-előkészítési és feladat-felszabadítási feladatok futtatása kötegelt számítási csomópontokon](./batch-job-prep-release.md).

- **Feladat-erőforrások fájljai**: Ha egy alkalmazás vagy adat csak egy adott feladathoz van jelentősége. Tegyük fel például, hogy öt tevékenységgel rendelkezik, amelyek mindegyike egy másik fájlt dolgoz fel, majd a kimenetet a blob Storage-ba írja.  Ebben az esetben a bemeneti fájlt meg kell adni a **feladatok erőforrás-fájlok** gyűjteményben, mert minden egyes feladathoz saját bemeneti fájl tartozik.

## <a name="determine-the-scope-required-of-a-file"></a>A fájlhoz szükséges hatókör meghatározása

Meg kell határoznia egy fájl hatókörét – egy készlethez, feladathoz vagy feladathoz szükséges fájl. A készletre hatókörben lévő fájloknak készlet-alkalmazáscsomag vagy indítási tevékenység használatát kell használniuk. A feladatra hatókörrel rendelkező fájloknak feladat-előkészítési feladatot kell használniuk. A készleten vagy a feladatok szintjén lévő fájlok jó példája az alkalmazások. A feladathoz tartozó fájloknak a feladat-erőforrás fájljait kell használniuk.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Az adatok kötegelt számítási csomópontokra való beolvasásának egyéb módjai

Az adatok olyan kötegelt számítási csomópontokra is beszerezhetők, amelyek nem vannak hivatalosan integrálva a Batch-REST APIba. Mivel Ön felügyeli Azure Batch csomópontokat, és futtathat egyéni végrehajtható fájlokat, tetszőleges számú egyéni forrásból kérhet le adatokat, amennyiben a Batch-csomópont hozzáfér a célhoz, és a forráshoz tartozó hitelesítő adatokkal rendelkezik a Azure Batch csomópontra. Néhány gyakori példa:

- Adatok letöltése az SQL-ből
- Adatok letöltése más webszolgáltatásokból/egyéni helyekről
- Hálózati megosztás hozzárendelése

### <a name="azure-storage"></a>Azure Storage tárterület

A blob Storage letöltési skálázhatósági célokat tartalmaz. Az Azure Storage fájlmegosztási skálázhatósági céljai megegyeznek egyetlen blob esetében. A méret hatással lesz a szükséges csomópontok és készletek számára.

