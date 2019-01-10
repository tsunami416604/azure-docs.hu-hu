---
title: Avere vFXT storage, Azure konfigurálása
description: A Avere vFXT egy háttér-tárolórendszer hozzáadása az Azure-hoz
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: a7036f6fbab771dc090e97034a6191cf82b707a7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190846"
---
# <a name="configure-storage"></a>A tárolás konfigurálása

Ebben a lépésben beállítja a háttérrendszer tárolórendszer vFXT-fürthöz tartozó.

> [!TIP]
> Ha használta a `create-cloudbacked-cluster` prototípus-szkript hozzon létre egy új blobtárolót a Avere vFXT fürttel, a tároló már be van állítva használatra, és nem kell hozzáadnia a storage együtt.
>
> Azonban ha az új blobtárolót alapértelmezett titkosítási kulccsal titkosított, vagy töltse le a kulcs-helyreállítási fájlt a fürt vagy kell adatok tárolása előtt cserélje le az alapértelmezett kulcs egy új kulcsot. Az alapértelmezett kulcs menti a rendszer csak a fürtöt, és nem lehet beolvasni, ha a fürt elveszik, vagy nem érhető el.
>
> Miután csatlakozott a Avere Vezérlőpult, kattintson a **beállítások** lapra, majd kattintson a **Core Filer** > **Felhőbeállítások titkosítási**. Az a **helyi kulcs Store** válassza az alábbi lehetőségek egyikét: 
> * Használja a **megjavít helyreállítási fájl** gombra kattintva a meglévő kulcs helyreállítás-fájljának beolvasása. A helyreállítási fájl és a fürt rendszergazdai jelszóval titkosított. Győződjön meg arról, hogy mentse a fájlt egy megbízható helyen. 
> * Kövesse az utasításokat a **hozzon létre egy új fő kulcsot** hozhat létre egy új titkosítási kulcs, hogy az oldal szakaszában. Ez a beállítás lehetővé teszi, hogy adjon meg egy egyedi jelszót, és töltse fel, és töltse le újra a helyreállítási fájl ellenőrzése a jelszófájlt pár van szükség.

Kövesse ezeket az utasításokat, ha használta a `create-minimal-cluster` prototípus-parancsfájl a fürthöz, vagy ha szeretne hozzáadni egy kiegészítő hardvert vagy a felhőalapú tárolási rendszer.

Nincsenek két fő feladatok:

1. [Hozzon létre egy alapvető filer](#create-a-core-filer), csatlakozik a vFXT fürtöt egy meglévő tároló rendszerben vagy egy Azure Storage-fiókot.

1. [Hozzon létre egy névteret csatlakozási](#create-a-junction), amely megadja, hogy az elérési út, hogy az ügyfelek fogja csatlakoztatni.

Ezek a lépések a Avere Vezérlőpult használják. Olvasási [a vFXT fürt eléréséhez](avere-vfxt-cluster-gui.md) megtudhatja, hogyan kell használni.

## <a name="create-a-core-filer"></a>Hozzon létre egy alapvető filer

"Core filer" kifejezés vFXT egy háttérrendszer tárolórendszer. A storage lehetnek például a NetApp vagy Isilon hardver NAS készülékek, vagy egy felhőalapú objektum tárolót. További információ a core kiemelik található [a Avere a fürt beállítások útmutató](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Egy mag filer hozzáadásához válassza ki a core kiemelik két fő objektumtípusok egyike:

  * [NAS alapvető filer](#nas-core-filer) – egy NAS core filer hozzáadása 
  * [Az Azure Storage fiók cloud alapvető filer](#azure-storage-account-cloud-core-filer) – ismerteti az Azure Storage-fiók hozzáadása a felhő alapvető filer

### <a name="nas-core-filer"></a>NAS core filer

A NAS-core filer lehet egy helyszíni NetApp vagy Isilon, vagy egy NAS-végpontot a felhőben.  
A tárolórendszer rendelkeznie kell a Avere vFXT fürtben – például egy 1 GB/s ExpressRoute-kapcsolat (nem egy VPN-) – egy megbízható nagy sebességű kapcsolatot, és azt a fürt legfelső szintű hozzáférést kell adnia a NAS-export használt.

Az alábbi lépéseket egy NAS core filer hozzáadása:

1. Avere a Vezérlőpulton kattintson a **beállítások** a felső fülön.

1. Kattintson a **Core Filer** > **kezelése Core kiemelik** a bal oldalon.

1. Kattintson a **Create** (Létrehozás) gombra.

   ![Lapjának képernyőképe, a bővítmény új core filer a kurzorral a Létrehozás gomb](media/avere-vfxt-add-core-filer-start.png)

1. Töltse ki a varázsló a szükséges adatokat: 

   * Adjon nevet a core filer.
   * Adjon meg egy teljesen minősített tartománynevét (FQDN), ha elérhető. Ellenkező esetben adja meg az IP-címet vagy állomásnevet, amelyet a core filer.
   * Válassza ki a filer osztály a listából. Ha nem tudja biztosan, válassza a **más**.

     ![Lapjának képernyőképe, a bővítmény új core filer core filer nevét és a teljes tartománynév](media/avere-vfxt-add-core-filer.png)
  
   * Kattintson a **tovább** , és válassza ki a gyorsítótár-szabályzatot. 
   * Kattintson a **Filer hozzáadása**.
   * Részletesebb információkért, tekintse meg [hozzáadása egy új NAS alapvető filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) a Avere a fürt beállítások útmutatója.

Ezután folytassa [hozzon létre egy elágazás](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Az Azure Storage felhőalapú core filer

Azure Blob storage-t a vFXT fürt háttérrendszer tárolóként használhassa, egy üres tárolót, amelyet egy alapvető filer kell.

> [!TIP] 
> A ``create-cloudbacked-cluster`` példaszkript létrehoz egy storage-tárolóba, határozza meg azt a core filer, és hoz létre a névteret a csatlakozási vFXT fürt létrehozásának részeként. A ``create-minimal-cluster`` minta parancsfájl nem hoz létre egy Azure storage-tárolóba. Ne kelljen létrehozni és konfigurálni egy Azure Storage-core filer a fürt létrehozása után, használja a ``create-cloudbacked-cluster`` parancsfájlt a vFXT fürt üzembe helyezéséhez.

Ezek a feladatok hozzáadása a fürthöz a Blob storage van szükség:

* Hozzon létre egy storage-fiókot (1. lépés, alább)
* Hozzon létre egy üres blobtárolót (2 – 3. lépést)
* A felhő hitelesítő adatainak a vFXT fürt (4 – 6. lépés), a tárelérési kulcs hozzáadása
* A Blob-tároló hozzáadása egy alapvető filer a vFXT fürt (7-9. lépés)
* Hozzon létre egy névteret szinkronizációs pont, amely az ügyfelek használatával csatlakoztathatja a core filer ([hozzon létre egy elágazás](#create-a-junction), hardver- és a felhőalapú tárolás azonos)

A fürt létrehozását követően a Blob storage hozzáadásához kövesse az alábbi lépéseket. 

1. Hozzon létre egy általános célú V2-tárfiók ezekkel a beállításokkal:

   * **Előfizetés** – ugyanaz, mint a vFXT fürt
   * **Erőforráscsoport** – ugyanaz, mint a vFXT fürtcsoporthoz (nem kötelező)
   * **Hely** – ugyanaz, mint a vFXT fürt
   * **Teljesítmény** – Standard (Premium storage jelenleg nem támogatott)
   * **Fiók típusa** -General-purpose V2 (StorageV2)
   * **Replikációs** – helyileg redundáns tárolás (LRS)
   * **Hozzáférési szint** – gyors elérésű
   * **Biztonságos átvitelre van szükség** -letiltja ezt a beállítást (nem alapértelmezett érték)
   * **Virtuális hálózatok** – nem kötelező megadni

   Az Azure Portalon, vagy kattintson az alábbi "Üzembe helyezése az Azure-bA" gombra.

   [![tárfiók létrehozása gomb](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. A fiók létrehozása után keresse meg a tárfiók oldalán.

   ![Új tárfiók Azure Portalon](media/avere-vfxt-new-storage-acct.png)

1. Hozzon létre egy blobtárolót kattintva **Blobok** az Áttekintés lapon, majd kattintson a **+ tároló**. Minden tároló nevét használja, és ellenőrizze, hogy hozzáférés van beállítva, **privát**.

   ![Nincsenek meglévő tárolók a blobok tárolólapot](media/avere-vfxt-blob-no-container.png)

1. Az Azure Storage-fiókkulcs beszerzése kattintva **hozzáférési kulcsok** alatt **beállítások**:

   ![Az Azure Portalon grafikus felhasználói felület a kulcs másolása](media/avere-vfxt-copy-storage-key.png) 

1. Nyissa meg a Avere Vezérlőpultot, a fürt számára. Kattintson a **beállítások**, majd nyissa meg **fürt** > **Felhőhöz tartozó hitelesítő adatok** a bal oldali navigációs ablaktáblán. Kattintson a Felhőhöz tartozó hitelesítő adatok lap **hitelesítő adatok hozzáadása**.

   ![A hitelesítő adatok hozzáadása gombra a Felhőhöz tartozó hitelesítő adatok konfigurálása lap](media/avere-vfxt-new-credential-button.png)

1. Töltse ki a felhő alapvető filer a hitelesítő adatok létrehozása a következő információkat: 

   | Mező | Érték |
   | --- | --- |
   | Hitelesítő adat neve | minden olyan leíró neve |
   | Szolgáltatás típusa | (válassza ki az Azure Storage elérési kulcs) |
   | Bérlő | Tárfiók neve |
   | Előfizetés | előfizetés-azonosító |
   | Tárelérési kulcs | Az Azure storage-fiókkulcs (az előző lépésben másolt) | 

   Kattintson a **Submit** (Küldés) gombra.

   ![Felhőbeli hitelesítő űrlap Avere Vezérlőpult](media/avere-vfxt-new-credential-submit.png)

1. Ezután hozza létre a core filer. A Avere Vezérlőpult a bal oldali menüjében kattintson **Core Filer** >  **kezelése Core kiemelik**. 

1. Kattintson a **létrehozás** gombot a **kezelése Core kiemelik** beállítások lapon.

1. Adja meg a varázsló:

   * Válassza ki a filer típus **felhőalapú**. 
   * Nevezze el az új core filer, és kattintson a **tovább**.
   * Fogadja el az alapértelmezett gyorsítótár-szabályzatot, és továbbra is a harmadik oldalon.
   * A **szolgáltatás típusa**, válassza a **az Azure storage**. 
   * Válassza ki a korábban létrehozott hitelesítő adatokat.
   * Állítsa be **tartalma gyűjtőhöz** való **üres**
   * Változás **tanúsítvány-ellenőrzési** való **letiltva**
   * Változás **tömörítési mód** való **None**  
   * Kattintson a **tovább**.
   * A tároló nevét adja meg a negyedik oldal az **gyűjtőbe neve** , *tárfiók_neve*/*container_name*.
   * Beállíthatja **titkosítási típus** való **None**.  Az Azure Storage alapértelmezés szerint titkosítva van.
   * Kattintson a **Filer hozzáadása**.

  Részletes információkat, olvassa el a [hozzáadása egy új felhőalapú core filer](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) a fürt konfiguráció Avere útmutatóban. 

Az oldal frissül, vagy frissítheti a lap az új core filer megjelenítéséhez.

A következő lépésben a [hozzon létre egy elágazás](#create-a-junction).

## <a name="create-a-junction"></a>A szinkronizációs pont létrehozása

A szinkronizációs pont egy elérési utat hoz létre az ügyfelek számára. Az ügyfelek csatlakoztatási az elérési útját, és az Ön által kiválasztott célhely érkeznek.

Hozzon létre például `/avere/files` leképezése a NetApp core filer `/vol0/data` exportálása és a `/project/resources` alkönyvtárat.

További információ a elhelyezni pontokra megtalálható a [Avere fürt konfigurációs útmutató névtér szakasza](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Kövesse az alábbi lépéseket a Avere Vezérlőpult-beállítások felületen:

* Kattintson a **VServer** > **Namespace** bal felső sarokban.
* Adjon meg egy névtér elérési út kezdetű / (perjel), például ``/avere/data``.
* Válassza ki a core filer.
* Válassza ki az alapvető filer exportálás.
* Kattintson a **tovább**.

  ![A szinkronizációs pont, a core filer és az exportálás befejeződött mezőkkel "Új szinkronizációs pont hozzáadása" oldalát bemutató képernyőkép](media/avere-vfxt-add-junction.png)

A szinkronizációs pont néhány másodperc múlva jelenik meg. Hozzon létre további elhelyezni pontokra, igény szerint.

Miután létrehozta a szinkronizációs pont, az ügyfelek is [a Avere vFXT fürt csatlakoztatási](avere-vfxt-mount-clients.md) férni a fájlrendszerhez.