---
title: Az Avere vFXT-tárhely konfigurálása - Azure
description: Háttértároló rendszer hozzáadása az Avere vFXT for Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252609"
---
# <a name="configure-storage"></a>A tárolás konfigurálása

Ez a lépés egy háttértároló rendszert állít be a vFXT-fürthöz.

> [!TIP]
> Ha létrehozott egy új Azure Blob-tárolót az Avere vFXT-fürttel együtt, a tároló már konfigurálva van, és készen áll a használatra.

Kövesse az alábbi utasításokat, ha nem hoz létre új Blob-tárolót a fürtjével, vagy ha további hardvert vagy felhőalapú tárolórendszert szeretne hozzáadni.

Két fő feladat van:

1. [Hozzon létre egy core filer,](#create-a-core-filer)amely összeköti a vFXT-fürt egy meglévő tárolórendszer vagy egy Azure Storage-fiók tároló.

1. [Hozzon létre egy névtércsomópontot,](#create-a-junction)amely meghatározza az ügyfelek által csatlakoztatni kívánt elérési utat.

Ezek a lépések az Avere vezérlőpultot használják. Olvassa [el a vFXT-fürt elérését](avere-vfxt-cluster-gui.md) a használat módjának megismeréséhez.

## <a name="create-a-core-filer"></a>Alapfájl létrehozása

A "Core filer" egy virtuális gép egy háttértároló rendszer vFXT kifejezése. A tároló lehet hardveres NAS-készülék, például NetApp vagy Isilon, vagy lehet felhőobjektum-tároló. Az alapvető fájlkezelőkről további információt az [Avere fürtbeállítási útmutatóban](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)talál.

Core filer hozzáadásához válasszon egyet a két fő típusú core filers:

* [NAS core filer](#nas-core-filer) - leírja, hogyan kell hozzáadni egy NAS core filer
* [Azure Storage-beli felhőalapú fájlkezelő](#azure-blob-storage-cloud-core-filer) – bemutatja, hogyan adhat hozzá egy Azure Blob-tárolót felhőalapú központi fájlkezelőként

### <a name="nas-core-filer"></a>NAS core filer

A NAS core filer lehet helyszíni NetApp vagy Isilon-készülék, vagy egy NAS-végpont a felhőben. A tárolórendszernek megbízható, nagy sebességű kapcsolattal kell rendelkeznie az Avere vFXT-fürthöz – például egy 1GBps ExpressRoute-kapcsolatnak (nem VPN-kapcsolatnak), és root hozzáférést kell biztosítania a fürtrootszámára a használt NAS-exportáláshoz.

A NAS-alapfájl hozzáadásához kövesse az alábbi lépéseket:

1. A Vezérlőpult on kattintson a **beállítások** fülre a tetején.

1. Kattintson a **Core Filer** > **Kezelése Core Filers** a bal oldalon.

1. Kattintson **a Létrehozás gombra.**

   ![Képernyőkép az Új core filer hozzáadása lapról, amelynek kurzora a Létrehozás gomb on](media/avere-vfxt-add-core-filer-start.png)

1. Töltse ki a szükséges adatokat a varázslóban:

   * Nevezze el a mag filer.
   * Adjon meg egy teljesen minősített tartománynevet (FQDN), ha rendelkezésre áll. Ellenkező esetben adjon meg egy IP-címet vagy állomásnevet, amely feloldódik a fő filer.
   * Válassza ki a filer osztálya a listából. Ha nem biztos benne, válassza az **Egyéb**lehetőséget.

     ![Képernyőkép az Új alapfájl hozzáadása lapról, amelynek neve a fájlfájl központi neve és teljesen minősített tartományneve](media/avere-vfxt-add-core-filer.png)
  
   * Kattintson a **Tovább** gombra, és válasszon egy gyorsítótár-házirendet.
   * Kattintson **a Fájlkezelő hozzáadása gombra.**
   * További információt az Új [NAS-alapfájl hozzáadása](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) az Avere fürtbeállítási útmutatóban talál.

Ezután folytassa [a Csomópont létrehozása című területcímű területcímű területcímű területcímű területcímű területcímű e-ponttal.](#create-a-junction)  

### <a name="azure-blob-storage-cloud-core-filer"></a>Az Azure Blob Storage felhőalapú központi fájlkezelője

Az Azure Blob storage használata a vFXT-fürt háttértárolójaként, egy üres tárolóra van szüksége, amelyet core filer-ként adhat hozzá.

A Blob storage fürthöz való hozzáadása a következő feladatokat igényli:

* Tárfiók létrehozása (1. lépés, lent)
* Üres Blob-tároló létrehozása (2-3. lépés)
* A tároló-hozzáférési kulcs hozzáadása a virtuális gép fürtfelhőhitelesítő adataiként (4–6. lépés)
* A Blob-tároló hozzáadása a vFXT-fürt alapvető fájlkezelőjeként (7-9. lépés)
* Névtér-csomópont létrehozása, amelyet az ügyfelek a core filer csatlakoztatására használnak ([Csomópont létrehozása](#create-a-junction), ugyanaz a hardveres és a felhőalapú tárolóhoz)

> [!TIP]
> Ha létrehoz egy új Blob-tárolót, amikor létrehoz egy Avere vFXT azure-fürthöz, a központi telepítési sablon automatikusan konfigurálja a tárolót, mint egy alapvető filer. (Ez akkor is igaz, ha a létrehozási parancsfájlt használja, amely kérésre elérhető.) Ezt követően nem kell konfigurálnia a fő fájlkezelőt.
>
> A fürtkészítő eszköz a következő konfigurációs feladatokat végzi:
>
> * Új Blob-tároló létrehozása a megadott tárfiókban
> * A tárolót core filer-ként határozza meg
> * Névtércsomópont létrehozása a tárolóhoz
> * Tárolási szolgáltatás végpontjának létrehozása a fürt virtuális hálózatán belül

Blob storage hozzáadása a fürt létrehozása után, kövesse az alábbi lépéseket.

1. Hozzon létre egy általános célú V2-tárfiókot az alábbi beállításokkal:

   * **Előfizetés** – megegyezik a vFXT-fürttel
   * **Erőforráscsoport** – megegyezik a vFXT-fürtcsoporttal (nem kötelező)
   * **Hely** - megegyezik a vFXT-fürttel
   * **Teljesítmény** – Standard (a prémium szintű tárhely nem támogatott)
   * **Számlafajta** - Általános célú V2 (StorageV2)
   * **Replikáció** – helyileg redundáns tárolás (LRS)
   * **Hozzáférési szint** – gyakori elérésű
   * **Biztonságos átvitel szükséges** - tiltsa le ezt az opciót (nem alapértelmezett érték)
   * **Virtuális hálózatok** - nem szükséges

   Használhatja az Azure Portalon, vagy kattintson az alábbi "Üzembe helyezés az Azure-ba" gombra.

   [![gomb a tárfiók létrehozásához](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. A fiók létrehozása után keresse meg a tárfiók lapját.

   ![Új tárfiók az Azure Portalon](media/avere-vfxt-new-storage-acct.png)

1. Új Blob-tároló létrehozása: Kattintson a **tárolók** elemre az áttekintő lapon, majd kattintson a **+Tároló**gombra. Használjon bármilyen tárolónevet, és győződjön meg arról, hogy a hozzáférés **privát.**

   ![Tárolási blobok lap a +konténer gomb bekarikázva, és egy új tároló jön létre egy előugró lapon](media/avere-vfxt-new-blob.png)

1. Az Azure Storage-fiók kulcsának beszerezése a Beállítások csoport **Hozzáférési kulcsaira** kattintva. **Settings** Másolja az egyik megadott kulcsot.

   ![Az Azure Portal grafikus felhasználói felülete a kulcs másolásához](media/avere-vfxt-copy-storage-key.png)

1. Nyissa meg a fürt Höz az Avere vezérlőpultot. Kattintson **a Beállítások****gombra,** majd nyissa meg a **fürtfelhő** > hitelesítő adatait a bal oldali navigációs ablakban. A Felhőbeli hitelesítő adatok lapon kattintson a **Hitelesítő adatok hozzáadása gombra.**

   ![Kattintson a Hitelesítő adatok hozzáadása gombra a Felhőbeli hitelesítő adatok konfigurációs lapján](media/avere-vfxt-new-credential-button.png)

1. Töltse ki az alábbi adatokat a felhőalapú központi fájlkezelő hitelesítő adatainak létrehozásához:

   | Mező | Érték |
   | --- | --- |
   | Hitelesítő adat neve | bármilyen leíró név |
   | Szolgáltatás típusa | (válassza az Azure Storage hozzáférési kulcsát) |
   | Bérlő | tárfiók neve |
   | Előfizetés | subscription ID |
   | Tárolási hozzáférési kulcs | Azure storage-fiók kulcsa (az előző lépésben másolva) |

   Kattintson a **Küldés gombra.**

   ![A felhőhitelesítő űrlap az Avere vezérlőpulton](media/avere-vfxt-new-credential-submit.png)

1. Ezután hozza létre a core filer. Az Avere vezérlőpult bal oldalán kattintson a **Core Filer** >  **Manage Core Filers**elemre.

1. Kattintson a **Létrehozás** gombra a **Core Filers-beállítások kezelése** lapon.

1. Töltse ki a varázslót:

   * Válassza a **Cloud**fájlkezelő típust.
   * Nevezze el az új főfájlkezelőt, és kattintson a **Tovább**gombra.
   * Fogadja el az alapértelmezett gyorsítótár-házirendet, és folytassa a harmadik oldalra.
   * A **Szolgáltatástípus csoportban**válassza az **Azure storage**lehetőséget.
   * Válassza ki a korábban létrehozott hitelesítő adatokat.
   * **A Gyűjtő tartalmának** **beállítása üresre**
   * **Tanúsítványellenőrzés** módosítása **letiltva**
   * **Tömörítési mód** módosítása **Nincs**
   * Kattintson a **Tovább** gombra.
   * A negyedik oldalon írja be a tároló nevét a **Gyűjtőnév** mezőbe *storage_account_name*/*container_name*.
   * Szükség esetén állítsa **a Titkosítás típust** Nincs **(Nincs)** beállításra.  Az Azure Storage alapértelmezés szerint titkosítva van.
   * Kattintson **a Fájlkezelő hozzáadása gombra.**

   További információkért olvassa el [Az új felhőalapú core filer hozzáadása](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) az Avere fürt konfigurációs útmutatójában című részt.

A lap frissül, vagy frissítheti az oldalt az új core filer megjelenítéséhez.

Ezután létre kell [hoznia egy csomópontot.](#create-a-junction)

## <a name="create-a-junction"></a>Csomópont létrehozása

A csomópont az ügyfelek számára létrehozott elérési út. Az ügyfelek csatlakoztatják az elérési utat, és megérkeznek a kiválasztott célhelyre.

Létrehozhat például `/vfxt/files` úgy, hogy leképezze a `/vol0/data` NetApp `/project/resources` core filer exportját és az alkönyvtárat.

A csomópontokról további információt [az Avere fürtkonfigurációs útmutató névtér szakaszában](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)talál.

Kövesse az alábbi lépéseket az Avere Vezérlőpult felületén:

* Kattintson a bal felső sarokban a > **VServer-névtér** elemre. **VServer**
* Adjon meg egy névtérelérési utat a ``/vfxt/data``/ (perjel) kezdetével kezdődően, például .
* Válassza ki az alapvető filer.
* Válassza ki a fő fájlexportálást.
* Kattintson a **Tovább** gombra.

  ![Képernyőkép az "Új csomópont hozzáadása" lapról, amelyen a csomóponthoz, a core filerhoz és az exportáláshoz kitöltött mezők láthatók](media/avere-vfxt-add-junction.png)

A csomópont néhány másodperc múlva megjelenik. Szükség szerint hozzon létre további csomópontokat.

A csomópont létrehozása után az ügyfelek a névtér elérési útját használják a tárolórendszerből származó fájlok eléréséhez.

## <a name="next-steps"></a>További lépések

* [Az Avere vFXT-fürt csatlakoztatása](avere-vfxt-mount-clients.md)
* Ismerje meg az [adatok új Blob-tárolóba való áthelyezésének](avere-vfxt-data-ingest.md) hatékony módjait
