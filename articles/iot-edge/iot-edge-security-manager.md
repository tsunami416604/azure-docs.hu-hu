---
title: Azure IoT Edge biztonsági menedzsere – Azure IoT Edge
description: Az IoT Edge-eszköz biztonsági álláspontjának és a biztonsági szolgáltatások integritásának kezelése.
services: iot-edge
keywords: biztonság, biztonságos elem, enklávé, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548696"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge biztonsági kezelő

Az Azure IoT Edge biztonsági kezelője egy jól kötött biztonsági mag az IoT Edge-eszköz és annak minden összetevője védelmére a biztonságos szilíciumhardver absztrakcióval. A biztonsági menedzser a biztonsági megerősítés fókuszpontja, és technológiai integrációs pontot biztosít az eredeti berendezésgyártók (OEM) számára.

![Azure IoT Edge biztonsági kezelő](media/edge-security-manager/iot-edge-security-manager.png)

Az IoT Edge biztonsági kezelője az IoT Edge-eszköz és az összes eredendő szoftverművelet integritásának védelmére törekszik. A biztonsági kezelő áttér a megbízhatósági alapoktól a megbízhatósági hardver ekkonyói gyökéréből (ha rendelkezésre áll), hogy az IoT Edge futásidejű indítási idejét állítsa be, és figyelje a folyamatban lévő műveleteket.  Az IoT Edge biztonsági kezelője olyan szoftver, amely biztonságos szilíciumhardverrel (ahol elérhető) együttműködve a lehető legmagasabb szintű biztonsági garanciákat nyújtja.  

Az IoT Edge biztonsági kezelőfeladatai közé tartoznak, de nem korlátozódnak a következőkre:

* Az Azure IoT Edge-eszköz biztonságos és mért rendszerindítása.
* Eszközidentitás-kiépítés és adott esetben a bizalmi kapcsolat váltása.
* A felhőszolgáltatások, például az Eszközkiépítési szolgáltatás eszközösszetevőinek üzemeltetése és védelme.
* Biztonságosan biztosítják az IoT Edge-modulokat egyedi identitásokkal.
* Forgalomirányító az eszköz hardvergyökér a bizalom a közjegyzői szolgáltatások.
* Az IoT Edge-műveletek integritásának figyelése futásidőben.

Az IoT Edge biztonsági kezelője három összetevőt tartalmaz:

* IoT Edge biztonsági démon.
* Hardveres biztonsági modul platform absztrakciós réteg (HSM PAL).
* Opcionális, de erősen ajánlott hardver szilícium gyökér a bizalom vagy a HSM.

## <a name="the-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon

Az IoT Edge biztonsági démon felelős az IoT Edge biztonsági kezelőlogikai műveleteiért. Az IoT Edge-eszköz megbízható számítási bázisának jelentős részét képviseli.

### <a name="design-principles"></a>Tervezési alapelvek

Az IoT Edge biztonsági démon két alapelvet követ: maximalizálja a működési integritást, és minimalizálja a felfújni és a lemorzsolódást.

#### <a name="maximize-operational-integrity"></a>Maximalizálja a működési integritást

Az IoT Edge biztonsági démon a lehető legnagyobb integritással működik a megbízhatósági hardverek adott gyökérrendszerének védelmi képességén belül. A megfelelő integráció, a gyökere a bizalom hardver intézkedések és figyeli a biztonsági démon statikusan és futásidőben, hogy ellenálljon a manipuláció.

A fizikai hozzáférés mindig fenyegetést jelent az IoT-eszközök számára. A megbízhatóság hardvergyökerezése fontos szerepet játszik az IoT Edge biztonsági démon integritásának védelmében.  Hardver gyökér bizalom jön két fajta:

* biztonságos elemek a bizalmas információk, például titkos kulcsok és kriptográfiai kulcsok védelmére.
* biztonságos enklávék a titkos kulcsok, például kulcsok és az olyan bizalmas számítási feladatok védelme érdekében, mint a mérés és a számlázás.

Kétféle végrehajtási környezet létezik a megbízhatóság hardvergyökerének használatához:

* A szabványos vagy gazdag végrehajtási környezet (REE), amely a bizalmas adatok védelmére szolgáló biztonságos elemek használatára támaszkodik.
* A megbízható végrehajtási környezet (TEE), amely a biztonságos enklávé technológia használatára támaszkodik a bizalmas információk védelme és a szoftvervégrehajtás védelme érdekében.

A biztonságos enklávékat a megbízhatóság hardvergyökereként használó eszközök esetében az IoT Edge biztonsági démonon belüli bizalmas logikának az enklávéban kell lennie.  A biztonsági démon nem érzékeny részei a TEE-n kívül is lehetnek.  Mindenesetre az eredeti tervezési gyártóknak (ODM) és az eredeti berendezésgyártóknak (OEM) ki kell terjeszteniük a HSM megbízhatóságát az IoT Edge biztonsági démon integritásának mérésére és védelmére a rendszerindításkor és a futásidőben.

#### <a name="minimize-bloat-and-churn"></a>Minimalizálja a felfújni és a lemorzsolódást

Az IoT Edge biztonsági démon egy másik alapelve a lemorzsolódás minimalizálása.  A legmagasabb szintű megbízhatóság érdekében az IoT Edge biztonsági démon szorosan párosítható az eszköz hardverének megbízhatósági gyökerével, és natív kódként működhet.  Gyakori, hogy az ilyen típusú megvalósítások frissítik a démonszoftvert a megbízhatóság biztonságos frissítési útvonalainak hardvergyökén keresztül (szemben az operációs rendszer által biztosított frissítési mechanizmusokkal), ami bizonyos esetekben kihívást jelenthet.  Bár az IoT-eszközök biztonsági megújítása ajánlott, a túlzott frissítési követelmények vagy a nagy frissítési hasznos terhelések számos módon bővíthetik a fenyegetési felületet.  Ilyen például a frissítések kihagyása a működési rendelkezésre állás maximalizálása érdekében, vagy a megbízhatósági hardver ek kontinitorsága túlságosan korlátozott a nagy frissítési adatok feldolgozásához.  Mint ilyen, az IoT Edge biztonsági démon kialakítása tömör, hogy a lábnyom és a megbízható számítástechnikai bázis kicsi maradjon, és minimalizálja a frissítési követelményeket.

### <a name="architecture-of-iot-edge-security-daemon"></a>Az IoT Edge biztonsági démon architektúrája

![Azure IoT Edge biztonsági démon](media/edge-security-manager/iot-edge-security-daemon.png)

Az IoT Edge biztonsági démon kihasználja a megbízhatósági technológia bármely rendelkezésre álló hardvergyökerét a biztonsági megerősítés érdekében.  Azt is lehetővé teszi a megosztott világműködés között egy szabványos / gazdag végrehajtási környezet (REE) és a megbízható végrehajtási környezet (TEE), ha a hardvertechnológiák kínálnak megbízható végrehajtási környezetben. A szerepkör-specifikus felületek lehetővé teszik az IoT Edge fő összetevői számára az IoT Edge-eszköz és műveletei integritásának biztosítását.

#### <a name="cloud-interface"></a>Felhőfelület

A felhőfelület lehetővé teszi, hogy az IoT Edge biztonsági démon a felhőszolgáltatások, például a felhő bókokat az eszköz biztonsága, mint a biztonsági megújítás.  Például az IoT Edge biztonsági démon jelenleg ezt az összeköttetést használja az Azure IoT [Hub-eszközkiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/) eléréséhez az eszköz identitáséletciklus-kezeléséhez.  

#### <a name="management-api"></a>Felügyeleti API

Az IoT Edge biztonsági démon egy felügyeleti API-t kínál, amelyet az IoT Edge-ügynök hív meg az IoT Edge-modul létrehozásakor/indításakor/leállításakor és eltávolításakor. A biztonsági démon tárolja a "regisztrációk" az összes aktív modul. Ezek a regisztrációk leképezhetik egy modul identitását a modul bizonyos tulajdonságaihoz. Példák: ezek a modul tulajdonságok közé tartozik a folyamat azonosítója (pid) a folyamat fut a tárolóban, és a docker-tároló tartalmának kivonatát.

Ezeket a tulajdonságokat a számítási feladatok API-ja (lásd alább) használja annak ellenőrzésére, hogy a hívó jogosult-e egy műveletre.

A felügyeleti API egy kiemelt API, csak az IoT Edge-ügynök hívható.  Mivel az IoT Edge biztonsági démon rendszerindítások, és elindítja az IoT Edge-ügynök, ellenőrzi, hogy az IoT Edge-ügynök nem illetéktelenül, majd létrehozhat egy implicit regisztrációt az IoT Edge-ügynök. Ugyanaz tetszési folyamat, amely a számítási feladatok API-t használ is korlátozza a felügyeleti API-t csak az IoT Edge-ügynök.

#### <a name="container-api"></a>Tároló API

A tároló API-t a modulkezeléshez használt tárolórendszerrel, például a Moby-val vagy a Docker-rel használja.

#### <a name="workload-api"></a>Számítási feladatok API

A számítási feladatok API-t minden modul számára elérhető. Bizonyítja az identitást, akár HSM gyökerező aláírt jogkivonatként, akár X509-tanúsítványként, és a megfelelő megbízhatósági csomagot egy modulhoz. A megbízhatósági csomag hitelesítésszolgáltatói tanúsítványokat tartalmaz az összes többi kiszolgálóhoz, amelyekben a moduloknak meg kell bízniuk.

Az IoT Edge biztonsági démon egy tanúsítványfolyamatot használ az API őrzéséhez. Amikor egy modul meghívja ezt az API-t, a biztonsági démon megpróbálja megtalálni az identitás regisztrációját. Ha sikeres, a regisztráció tulajdonságait használja a modul méréséhez. Ha a mérési folyamat eredménye megegyezik a regisztrációval, új személyazonosság-igazolás jön létre. A megfelelő hitelesítésszolgáltatói tanúsítványok (megbízhatósági csomag) visszakerülnek a modulba.  A modul ezt a tanúsítványt használja az IoT Hubhoz, más modulokhoz való csatlakozáshoz vagy egy kiszolgáló elindításához. Amikor az aláírt jogkivonat vagy tanúsítvány lejár, a modul felelőssége, hogy új tanúsítványt kérjen.

### <a name="integration-and-maintenance"></a>Integráció és karbantartás

A Microsoft fenntartja az [IoT Edge biztonsági démon fő kódbázisát a GitHubon.](https://github.com/Azure/iotedge/tree/master/edgelet)

#### <a name="installation-and-updates"></a>Telepítés és frissítések

Az IoT Edge biztonsági démon telepítése és frissítései az operációs rendszer csomagkezelő rendszerén keresztül kezelhetők. A hardveres megbízhatósági gyökerű IoT Edge-eszközöknek további megerősítést kell biztosítaniuk a démon integritásához azáltal, hogy az életciklusát a biztonságos rendszerindítási és frissítési felügyeleti rendszereken keresztül kezelik. Az eszközgyártóknak a megfelelő eszközképességeik alapján kell felfedezniük ezeket az utakat.

#### <a name="versioning"></a>Verziókezelés

Az IoT Edge futásidejű nyomon követi és jelenti az IoT Edge biztonsági démon verzióját. A verzió az IoT Edge-ügynökmodul jelentett tulajdonságának *runtime.platform.version* attribútumaként van jelentve.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Hardveres biztonsági modul platformabsztrakciós réteg (HSM PAL)

A HSM PAL absztrakt minden gyökere a megbízhatósági hardver elkülöníteni a fejlesztő vagy a felhasználó az IoT Edge azok összetettségét.  Alkalmazásprogramozási felület (API) és a tartományok közötti kommunikációs eljárások kombinációját foglalja magában, például a szabványos végrehajtási környezet és a biztonságos enklávé közötti kommunikációt.  A HSM PAL tényleges megvalósítása a használatban lévő biztonságos hardvertől függ. Létezése lehetővé teszi gyakorlatilag bármilyen biztonságos szilíciumhardver használatát.

## <a name="secure-silicon-root-of-trust-hardware"></a>A megbízhatósági hardver szilíciumgyökei

Biztonságos szilícium szükséges a megbízhatóság rögzítéséhez az IoT Edge eszköz hardver.  Biztonságos szilícium jön a fajta közé Trusted Platform Module (TPM), beágyazott Secure Element (eSE), ARM TrustZone, Intel SGX, és az egyéni biztonságos szilícium technológiák.  Az eszközök ben lévő biztonságos szilíciumgyökér használata ajánlott, tekintettel az IoT-eszközök fizikai hozzáférhetőségével kapcsolatos fenyegetésekre.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge biztonsági menedzser integrációja és karbantartása

Az IoT Edge biztonsági kezelőcélja, hogy azonosítsa és elkülönítse azokat az összetevőket, amelyek védik az Azure IoT Edge platform biztonságát és integritását az egyéni edzéshez. A harmadik feleknek, például az eszközgyártóknak, az eszközhardverükkel elérhető egyéni biztonsági funkciókat kell használniuk.  Tekintse meg a következő lépéseket szakaszban a hivatkozásokat, amelyek bemutatják, hogyan lehet megkeményíteni az Azure IoT biztonsági kezelőjét a platformmegbízhatósági modullal (TPM) Linux és Windows platformokon. Ezek a példák szoftvereket vagy virtuális TPM-eket használnak, de közvetlenül a különálló TPM-eszközök használatára vonatkoznak.  

## <a name="next-steps"></a>További lépések

Olvassa el a blog [biztosítása az intelligens szélén](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

Hozzon létre és hozzon létre [egy IoT Edge-eszközt egy virtuális TPM-mel egy Linux virtuális gépen.](how-to-auto-provision-simulated-device-linux.md)

IoT Edge-eszközt hozhat létre és [létesíthet szimulált TPM-mel Windows rendszeren.](how-to-auto-provision-simulated-device-windows.md)
