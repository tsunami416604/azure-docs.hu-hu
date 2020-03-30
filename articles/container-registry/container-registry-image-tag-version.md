---
title: A képcímkével kapcsolatos gyakorlati tanácsok
description: Gyakorlati tanácsok a Docker-tárolórendszerképek címkézéséhez és verziószámozásához, amikor lemezképeket kér le és kér le egy Azure-tároló beállításjegyzékéből
author: stevelasker
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: b483317960409fe1fbea181706f12375606fe659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445736"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>Javaslatok a tárolórendszerképek címkézéséhez és verziószámozásához

A tárolórendszerképek lenyomása egy tároló beállításjegyzékbe, majd üzembe helyezésekor szüksége van egy stratégia a rendszerkép címkézése és verziószámozása. Ez a cikk két megközelítést és azt ismerteti, hogy az egyes körülmények hol illeszkednek a tároló életciklusa során:

* **Stabil címkék** – Címkék, amelyeket újra felhasznál, például egy fő- vagy alverzió, például *a mycontainerimage:1.0*jelű verzió t.
* **Egyedi címkék** - A rendszerleíró adatbázisba leadott minden egyes lemezképhez más-más címke, például *mycontainerimage:abc123*.

## <a name="stable-tags"></a>Stabil címkék

**Javaslat:** Használjon stabil címkéket a tárolóbuildek **alaplemezképeinek** karbantartásához. Kerülje a stabil címkékkel rendelkező központi telepítések, mert ezek a címkék továbbra is frissítéseket kapnak, és inkonzisztenciákat okozhatnak az éles környezetekben.

*A stabil címkék* azt jelentik, hogy egy fejlesztő vagy egy buildrendszer továbbra is lehúzhat egy adott címkét, amely továbbra is frissítéseket kap. A stabil nem jelenti azt, hogy a tartalma le van fagyasztva. Inkább stabil azt jelenti, hogy a kép stabilnak kell lennie a szándékkal, hogy a változat. Ha "stabil" marad, előfordulhat, hogy a biztonsági javítások vagy keretrendszer-frissítések alkalmazásához kell szervizelni.

### <a name="example"></a>Példa

A keretcsapat az 1.0-s verziót szállítja. Tudják, hogy a frissítéseket, beleértve a kisebb frissítéseket is, szállítani fogják. Egy adott fő- és alverzió stabil címkéinek támogatásához két stabil címkekészlettel rendelkeznek.

* `:1`– a főverzió stabil címkéje. `1`a "legújabb" vagy a "legújabb" 1.* verziót jelöli.
* `:1.0`- stabil címke az 1.0-s verzióhoz, amely lehetővé teszi a fejlesztő számára, hogy kötődjön az 1.0 frissítéseihez, és ne gördült előre az 1.1-re, amikor megjelenik.

A csapat is `:latest` használja a címkét, amely rámutat, hogy a legújabb stabil tag, nem számít, mi az aktuális fő verzió.

Amikor alaplemezkép-frissítések érhetők el, vagy a keretrendszer bármilyen karbantartási kiadása, a stabil címkékkel rendelkező képek frissülnek a legújabb kivonatra, amely az adott verzió legfrissebb stabil kiadását képviseli.

Ebben az esetben mind a fő- és a kisebb címkék folyamatosan szervizelnek. Egy alaplemezkép-forgatókönyvből ez lehetővé teszi, hogy a rendszerkép tulajdonosa kiszolgált lemezképeket biztosítson.

### <a name="delete-untagged-manifests"></a>Címkézetlen jegyzékek törlése

Ha egy stabil címkével rendelkező kép frissül, a korábban címkézett kép címkézetlen lesz, ami árva képet eredményez. Az előző lemezkép jegyzékfájlja és egyedi rétegadatai a beállításjegyzékben maradnak. A rendszerleíró adatbázis méretének megőrzése érdekében rendszeresen törölheti a stabil képfrissítésekből származó címkézetlen jegyzékeket. Például [automatikusan kiüríti](container-registry-auto-purge.md) a címkézetlen jegyzékek jegyzékek régebbi, mint egy megadott időtartam, vagy hozzon létre [egy adatmegőrzési házirend](container-registry-retention-policy.md) et címkézetlen jegyzékek.

## <a name="unique-tags"></a>Egyedi címkék

**Javaslat:** Használjon egyedi címkéket **a központi telepítések,** különösen olyan környezetben, amely több csomóponton skálázható. Valószínűleg az összetevők konzisztens verziójának szándékos telepítését szeretné. Ha a tároló újraindul, vagy egy orchestrator horizontális felskálázása több példányt, a gazdagépek nem véletlenül lekérése egy újabb verzió, inkonzisztens a többi csomópont.

Az egyedi címkézés egyszerűen azt jelenti, hogy minden kép nekik van egy egyedi címke. A címkéket nem használják fel újra. Számos minta követheti az egyedi címkék létrehozásához, többek között:

* **Dátum-idő bélyegző** - Ez a megközelítés meglehetősen gyakori, mivel egyértelműen meg tudja mondani, hogy mikor készült a kép. De, hogyan kell korrelálni, hogy vissza a build rendszer? Meg kell találnod a buildet, ami egyszerre készült el? Melyik időzónában vagy? Az összes buildrendszer az UTC-re van kalibrálva?
* **Git véglegesítése** – Ez a megközelítés addig működik, amíg el nem kezdi az alaprendszerkép-frissítések támogatását. Ha egy alaprendszerfrissítés történik, a buildrendszer ugyanazzal a Git véglegesítéssel indul, mint az előző build. Az alapkép azonban új tartalommal rendelkezik. Általában a Git véglegesítés *félstabil*címkét biztosít.
* **Manifest kivonatolás** – minden tárolórendszerkép egy tároló beállításjegyzékbe van társítva egy jegyzékfájl, egyedi SHA-256 kivonattal azonosított, vagy kivonatolni. Bár egyedi, a kivonat hosszú, nehezen olvasható, és nem függ össze a build környezetben.
* **Build-azonosító** – Ez a lehetőség lehet a legjobb, mivel valószínűleg növekményes, és lehetővé teszi, hogy korrelál vissza az adott build, hogy megtalálja az összes összetevőt és naplókat. Azonban, mint egy nyilvánvaló emészthető, lehet, hogy nehéz egy ember olvasni.

  Ha a szervezet több buildrendszerrel rendelkezik, a címke előrögzítése `<build-system>-<build-id>`a buildrendszer nevével a következő változat: . Például megkülönböztetheti a buildeket az API-csapat Jenkins buildrendszerétől és a webcsapat Azure Pipelines buildrendszerétől.

### <a name="lock-deployed-image-tags"></a>Telepített lemezképek címkéinek zárolása

Ajánlott eljárásként azt javasoljuk, hogy [zárolja](container-registry-image-lock.md) az üzembe `write-enabled` helyezett képcímkét, ha az attribútumát a-ra `false`állítja. Ez a gyakorlat megakadályozza, hogy véletlenül eltávolítson egy lemezképet a beállításjegyzékből, és esetleg megzavarja a központi telepítéseket. A zárolási lépést felveheti a kiadási folyamatba.

Az üzembe helyezett lemezképek zárolása továbbra is lehetővé teszi, hogy más, nem központiított lemezképeket távolítson el a rendszerleíró adatbázisból az Azure Container Registry szolgáltatásaival a beállításjegyzék karbantartása érdekében. Például [automatikusan kiüríti](container-registry-auto-purge.md) a címkézetlen jegyzékfájljait vagy a feloldott képeket, amelyek régebbiek egy adott időtartamnál, vagy [adatmegőrzési házirendet](container-registry-retention-policy.md) állíthat be a címkézetlen jegyzékekhez.

## <a name="next-steps"></a>További lépések

A cikkben szereplő fogalmak részletesebb megvitatását a [Docker-címkézés: A docker-képek címkézésével és verziószámozásával kapcsolatos gyakorlati tanácsok](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)című témakörben talál.

Az Azure container registry teljesítményének és költséghatékony használatának maximalizálása érdekében olvassa el az Azure Container Registry gyakorlati tanácsok című [témakört.](container-registry-best-practices.md)

<!-- IMAGES -->


<!-- LINKS - Internal -->

