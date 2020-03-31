---
title: X.509-es tanúsítványok rollja az Azure IoT Hub-eszközkiépítési szolgáltatásában
description: X.509-es tanúsítványok görgetése az eszközkiépítési szolgáltatás (DPS) példányával
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 4d5ddb229cd6a41235990437bc0f8db08e3381ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974887"
---
# <a name="how-to-roll-x509-device-certificates"></a>Az X.509-es eszköztanúsítványok görgetése

Az IoT-megoldás életciklusa során szüksége lesz a tanúsítványok roll. A tanúsítványok üzembe lépésének két fő oka a biztonság megsértése és a tanúsítványok lejárata. 

A folyamatos működésbe hozó tanúsítványok biztonsági gyakorlat, amely nek köszönhetően a rendszer biztonsága érdekében, ha a rendszer megsértése esetén. A [Assume Breach Methodology](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)részeként a Microsoft támogatja a reaktív biztonsági folyamatok szükségességét a megelőző intézkedésekkel együtt. Az eszköztanúsítványok gördülését a biztonsági folyamatok részét kell foglalni. A tanúsítványok görgetésének gyakorisága a megoldás biztonsági igényeitől függ. A rendkívül érzékeny adatokat tartalmazó megoldásokkal rendelkező ügyfelek naponta elforgathatják a tanúsítványt, míg mások néhány évente görgethetik tanúsítványaikat.

Az eszközállapot-legördülő tanúsítványok az eszközön és az IoT hubon tárolt tanúsítvány frissítését jelentik. Ezt követően az eszköz újra üzembe helyezheti magát az IoT hubbal az eszközkiépítési szolgáltatással való normál [automatikus kiépítés](concepts-auto-provisioning.md) használatával.


## <a name="obtain-new-certificates"></a>Új tanúsítványok beszerzése

Az IoT-eszközökhöz számos módon szerezhet be új tanúsítványokat. Ezek közé tartozik a tanúsítványok beszerzése az eszközgyárból, a saját tanúsítványok létrehozása, valamint egy harmadik fél általi kezelés a tanúsítványok létrehozásához. 

A tanúsítványokat egymás írják alá, hogy megbízhatósági láncot alkossanak a legfelső szintű hitelesítésszolgáltatói tanúsítványtól a [levéltanúsítványig.](concepts-security.md#end-entity-leaf-certificate) Az aláíró tanúsítvány az a tanúsítvány, amely a levéltanúsítvány aláírásához használható a megbízhatósági lánc végén. Az aláíró tanúsítvány lehet legfelső szintű hitelesítésszolgáltatói tanúsítvány vagy köztes tanúsítvány a megbízhatósági láncban. További információ: [X.509 certificates](concepts-security.md#x509-certificates).
 
Az aláíró tanúsítványok beszerzésének két különböző módja van. Az éles rendszerek számára ajánlott első módszer az aláíró tanúsítvány megvásárlása egy legfelső szintű hitelesítésszolgáltatótól. Így a biztonság megbízható forrásra van leláncolni. 

A második módszer az, hogy saját X.509 tanúsítványokat hozzon létre egy olyan eszközzel, mint az OpenSSL. Ez a megközelítés kiválóan alkalmas az X.509-es tanúsítványok tesztelésére, de kevés garanciát nyújt a biztonsággal. Azt javasoljuk, hogy csak akkor használja ezt a módszert a teszteléshez, ha nem készült fel arra, hogy saját hitelesítésszolgáltatójaként működjön.
 

## <a name="roll-the-certificate-on-the-device"></a>A tanúsítvány görgetése az eszközön

Az eszközön lévő tanúsítványokat mindig biztonságos helyen, például [hardveres biztonsági modulban (HSM)](concepts-device.md#hardware-security-module)kell tárolni. Az eszköztanúsítványok görgetésének módja elsősorban attól függ, hogyan hozták létre és telepítették őket az eszközökre. 

Ha a tanúsítványokat egy harmadik féltől szerezte, meg kell vizsgálnia, hogyan tekercselik a tanúsítványokat. A folyamat lehet, hogy szerepel a megállapodás velük, vagy lehet, hogy egy külön szolgáltatást kínálnak. 

Ha saját eszköztanúsítványait kezeli, saját folyamatot kell létrehoznia a tanúsítványok frissítéséhez. Győződjön meg arról, hogy mind a régi, mind az új levéltanúsítványok közös neve (KN). Ugyanazzal a KN-vel az eszköz újra üzembe helyezheti magát anélkül, hogy ismétlődő regisztrációs rekordot hozna létre. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>A tanúsítvány gördülékenyítése az IoT-központban

Az eszköztanúsítvány manuálisan hozzáadható egy IoT-központhoz. A tanúsítvány egy eszközkiépítési szolgáltatáspéldány használatával is automatizálható. Ebben a cikkben feltételezzük, hogy egy eszközkiépítési szolgáltatáspéldány automatikus kiépítés támogatására használatos.

Ha egy eszköz először kiépítés automatikus kiépítése, ez elindul, és kapcsolatba lép a létesítési szolgáltatás. A kiépítési szolgáltatás válaszol egy identitás-ellenőrzés végrehajtásával, mielőtt egy eszköz identitását egy IoT hub használatával az eszköz levél tanúsítványa a hitelesítő adat. A létesítési szolgáltatás ezután közli az eszközzel, hogy melyik IoT hubhoz van rendelve, és az eszköz ezután a levéltanúsítványt használja az IoT hub hitelesítéséhez és az IoT hubhoz való csatlakozáshoz. 

Miután egy új levéltanúsítvány gördült az eszközre, már nem tud csatlakozni az IoT hubhoz, mert egy új tanúsítványt használ a csatlakozáshoz. Az IoT hub csak felismeri az eszközt a régi tanúsítvánnyal. Az eszköz csatlakozási kísérletének eredménye "jogosulatlan" csatlakozási hiba lesz. A hiba megoldásához frissítenie kell az eszköz regisztrációs bejegyzését, hogy figyelembe vegye az eszköz új levéltanúsítványát. Ezután a kiépítési szolgáltatás szükség szerint frissítheti az IoT Hub-eszköz beállításjegyzék-adatait, amikor az eszközt újra kiépíti. 

A kapcsolathibája alól egy lehetséges kivétel egy olyan forgatókönyv, amelyben létrehozott egy [regisztrációs csoportot](concepts-service.md#enrollment-group) az eszközhöz a létesítési szolgáltatásban. Ebben az esetben, ha nem a legfelső szintű vagy köztes tanúsítványok at az eszköz tanúsítványlánc megbízhatósági lánc, majd az eszköz lesz felismerve, ha az új tanúsítvány része a bizalmi lánc a regisztrációs csoportban meghatározott. Ha ez a forgatókönyv a biztonság megsértésére adott reakcióként merül fel, legalább feketelistára kell tenni a csoport azon konkrét eszköztanúsítványait, amelyek et megsértve kell tekinteni. További információ: [Blacklist adott eszközök egy regisztrációs csoportban](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

A gördült tanúsítványok igénylési bejegyzéseinek frissítése a **Regisztrációk kezelése** lapon történik. A lap eléréséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT Hub-eszközkiépítési szolgáltatás példányát, amely rendelkezik az eszköz regisztrációs bejegyzésével.

2. Kattintson a **Regisztrációk kezelése** elemre.

    ![Regisztrációk kezelése](./media/how-to-roll-certificates/manage-enrollments-portal.png)


A regisztrációs bejegyzés frissítésének kezelése attól függ, hogy egyéni beléptetéseket vagy csoportos beléptetéseket használ.How you handle updating the enrollment entry will depend to whether you're using individual enrollments, or group enrollments. Az ajánlott eljárások attól függően is eltérnek, hogy a biztonsági előírások megsértése vagy a tanúsítvány lejárata miatt gördülékeny tanúsítványokat vezet be. A következő szakaszok ismertetik, hogyan kell kezelni ezeket a frissítéseket.


## <a name="individual-enrollments-and-security-breaches"></a>Egyéni regisztrációk és biztonsági rések

Ha a tanúsítványokat egy biztonsági rés miatt vezeti be, a következő módszert kell alkalmaznia, amely azonnal törli az aktuális tanúsítványt:

1. Kattintson **az Egyéni regisztrációk gombra,** majd a listában a regisztrációs azonosító bejegyzésére. 

2. Kattintson az **Aktuális tanúsítvány törlése** gombra, majd a mappa ikonra kattintva jelölje ki a regisztrációs bejegyzéshez feltöltendő új tanúsítványt. Ha végzett, kattintson a **Mentés** gombra.

    Ezeket a lépéseket az elsődleges és másodlagos tanúsítvány esetében kell végrehajtani, ha mindkettő biztonsága sérül.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Miután a feltört tanúsítványt eltávolították a létesítési szolgáltatásból, a tanúsítvány továbbra is használható eszközkapcsolatok az IoT hub, amíg egy eszköz regisztrációja ott létezik. Ezt kétféleképpen oldhatja meg: 

    Az első út az lenne, hogy manuálisan navigáljon az IoT hubhoz, és azonnal távolítsa el a sérült tanúsítványhoz társított eszközregisztrációt. Ezután amikor az eszköz ismét rendelkezik egy frissített tanúsítvánnyal, új eszközregisztráció jön létre.     

    ![IoT hub-eszközregisztráció eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második út az lenne, hogy a reprovisioning támogatás újbóli üzembe helyezheti az eszközt ugyanarra az IoT hubra. Ez a megközelítés az IT hubon történő eszközregisztráció tanúsítványának cseréjére használható. További információ: [Eszközök újbóli kiépítése.](how-to-reprovision.md)

## <a name="individual-enrollments-and-certificate-expiration"></a>Egyéni beléptetések és tanúsítványok lejárata

Ha a tanúsítványok lejáratának kezeléséhez folyamatos tanúsítványokat vezet be, a másodlagos tanúsítvány konfigurációját az alábbiak szerint kell használnia a kiépítésre próbáló eszközök állásidejének csökkentéséhez.

Később, amikor a másodlagos tanúsítvány is lejár, és meg kell hengerelt, az elsődleges konfiguráció használatával forgatható. Az elsődleges és a másodlagos tanúsítványok közötti váltás ily módon csökkenti az üzembe építést megkísérelt eszközök állásidejét.


1. Kattintson **az Egyéni regisztrációk gombra,** majd a listában a regisztrációs azonosító bejegyzésére. 

2. Kattintson a **Másodlagos tanúsítvány** elemre, majd a mappa ikonra kattintva jelölje ki a regisztrációs bejegyzéshez feltöltendő új tanúsítványt. Kattintson a **Mentés** gombra.

    ![Egyéni beléptetések kezelése a másodlagos tanúsítvánnyal](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Később, amikor az elsődleges tanúsítvány lejárt, jöjjön vissza, és törölje az elsődleges tanúsítványt az **aktuális tanúsítvány törlése** gombra kattintva.

## <a name="enrollment-groups-and-security-breaches"></a>Beléptetési csoportok és biztonsági rések

Ha egy biztonsági rést megsértő csoportregisztrációt szeretne frissíteni, az alábbi módszerek egyikét kell használnia, amelyek azonnal törlik az aktuális legfelső szintű hitelesítésszolgáltatót vagy köztes tanúsítványt.

#### <a name="update-compromised-root-ca-certificates"></a>A sérült legfelső szintű hitelesítésszolgáltatói tanúsítványok frissítése

1. Kattintson az Eszközkiépítési szolgáltatáspéldány **Tanúsítványok** fülére.

2. Kattintson a sérült tanúsítványra a listában, majd kattintson a **Törlés** gombra. Erősítse meg a törlést a tanúsítvány nevének megadásával, majd kattintson az **OK**gombra. Ismételje meg ezt a folyamatot az összes feltört tanúsítvány esetében.

    ![Gyökérhitelesítési tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)

3. Az [ellenőrzött hitelesítésszolgáltatói tanúsítványok konfigurálása](how-to-verify-certificates.md) és ellenőrzése című részben ismertetett lépéseket követve új legfelső szintű hitelesítésszolgáltatói tanúsítványokat adhat hozzá és ellenőrizhet.

4. Kattintson az Eszközkiépítési szolgáltatáspéldány **regisztrációk kezelése** fülére, majd a **Regisztrációs csoportok listára.** Kattintson a regisztrációs csoport nevére a listában.

5. Kattintson **a hitelesítésszolgáltatói tanúsítvány**elemre, és válassza ki az új legfelső szintű hitelesítésszolgáltatói tanúsítványt. Ezután kattintson a **Mentés gombra.** 

    ![Az új legfelső szintű hitelesítésszolgáltatói tanúsítvány kijelölése](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Miután a feltört tanúsítványt eltávolították a létesítési szolgáltatásból, a tanúsítvány továbbra is használható eszközkapcsolatok az IoT hub, amíg az eszköz regisztrációk ott létezik. Ezt kétféleképpen oldhatja meg: 

    Az első út az lenne, hogy manuálisan navigáljon az IoT hubhoz, és azonnal távolítsa el a sérült tanúsítványhoz társított eszközregisztrációt. Ezt követően, amikor az eszközök kiépítése ismét frissített tanúsítványokat, egy új eszköz regisztráció jön létre mindegyikhez.     

    ![IoT hub-eszközregisztráció eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második út az lenne, hogy a reprovisioning támogatás újbóli üzembe helyezheti az eszközöket ugyanarra az IoT hubra. Ez a megközelítés az IT hubon lévő eszközregisztrációk tanúsítványainak helyettesítésére használható. További információ: [Eszközök újbóli kiépítése.](how-to-reprovision.md)



#### <a name="update-compromised-intermediate-certificates"></a>A feltört köztes tanúsítványok frissítése

1. Kattintson **a Regisztrációs csoportok gombra,** majd a listában a csoport nevére. 

2. Kattintson **a Köztes tanúsítvány**és **az Aktuális tanúsítvány törlése parancsra.** Kattintson a mappa ikonra a regisztrációs csoport új köztes tanúsítványának megugrásához. Ha végzett, kattintson a **Mentés** gombra. Ezeket a lépéseket mind az elsődleges, mind a másodlagos tanúsítvány esetében el kell végezni, ha mindkettő biztonsága sérül.

    Ezt az új köztes tanúsítványt egy ellenőrzött legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal kell aláírni, amely már hozzá lett adva a kiépítési szolgáltatáshoz. További információ: [X.509 certificates](concepts-security.md#x509-certificates).

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Miután a feltört tanúsítványt eltávolították a létesítési szolgáltatásból, a tanúsítvány továbbra is használható eszközkapcsolatok az IoT hub, amíg az eszköz regisztrációk ott létezik. Ezt kétféleképpen oldhatja meg: 

    Az első út az lenne, hogy manuálisan navigáljon az IoT hubhoz, és azonnal távolítsa el a sérült tanúsítványhoz társított eszközregisztrációt. Ezt követően, amikor az eszközök kiépítése ismét frissített tanúsítványokat, egy új eszköz regisztráció jön létre mindegyikhez.     

    ![IoT hub-eszközregisztráció eltávolítása](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második út az lenne, hogy a reprovisioning támogatás újbóli üzembe helyezheti az eszközöket ugyanarra az IoT hubra. Ez a megközelítés az IT hubon lévő eszközregisztrációk tanúsítványainak helyettesítésére használható. További információ: [Eszközök újbóli kiépítése.](how-to-reprovision.md)


## <a name="enrollment-groups-and-certificate-expiration"></a>Igénylési csoportok és tanúsítvány lejárata

Ha a tanúsítványok lejáratának kezeléséhez folyamatos tanúsítványokat vezet be, a másodlagos tanúsítvány konfigurációját az alábbiak szerint kell használnia annak biztosításához, hogy a kiépítést megkísérlő eszközök ne legyenek állásidő.

Később, amikor a másodlagos tanúsítvány is lejár, és meg kell hengerelt, az elsődleges konfiguráció használatával forgatható. Az elsődleges és másodlagos tanúsítványok közötti váltás ily módon biztosítja, hogy nincs állásidő az üzembe létesíteni próbáló eszközök számára. 

#### <a name="update-expiring-root-ca-certificates"></a>Lejáró legfelső szintű hitelesítésszolgáltatói tanúsítványok frissítése

1. Az [ellenőrzött hitelesítésszolgáltatói tanúsítványok konfigurálása](how-to-verify-certificates.md) és ellenőrzése című részben ismertetett lépéseket követve új legfelső szintű hitelesítésszolgáltatói tanúsítványokat adhat hozzá és ellenőrizhet.

2. Kattintson az Eszközkiépítési szolgáltatáspéldány **regisztrációk kezelése** fülére, majd a **Regisztrációs csoportok listára.** Kattintson a regisztrációs csoport nevére a listában.

3. Kattintson **a hitelesítésszolgáltatói tanúsítvány**elemre, és válassza ki az új legfelső szintű hitelesítésszolgáltatói tanúsítványt a **Másodlagos tanúsítvány konfigurációja** alatt. Ezután kattintson a **Mentés gombra.** 

    ![Az új legfelső szintű hitelesítésszolgáltatói tanúsítvány kijelölése](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Később, amikor az elsődleges tanúsítvány lejárt, kattintson az Eszközkiépítési szolgáltatáspéldány **Tanúsítványok** fülére. Kattintson a lejárt tanúsítványra a listában, majd a **Törlés** gombra. Erősítse meg a törlést a tanúsítvány nevének megadásával, majd kattintson az **OK**gombra.

    ![Gyökérhitelesítési tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Lejáró köztes tanúsítványok frissítése


1. Kattintson **a Regisztrációs csoportok gombra,** majd a listában a csoport nevére. 

2. Kattintson a **Másodlagos tanúsítvány** elemre, majd a mappa ikonra kattintva jelölje ki a regisztrációs bejegyzéshez feltöltendő új tanúsítványt. Kattintson a **Mentés** gombra.

    Ezt az új köztes tanúsítványt egy ellenőrzött legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal kell aláírni, amely már hozzá lett adva a kiépítési szolgáltatáshoz. További információ: [X.509 certificates](concepts-security.md#x509-certificates).

   ![Egyéni beléptetések kezelése a másodlagos tanúsítvánnyal](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Később, amikor az elsődleges tanúsítvány lejárt, jöjjön vissza, és törölje az elsődleges tanúsítványt az **aktuális tanúsítvány törlése** gombra kattintva.


## <a name="reprovision-the-device"></a>Az eszköz újbóli kiépítése

Miután a tanúsítvány gördült mind az eszközön, mind az eszközkiépítési szolgáltatás, az eszköz újra kiépítheti magát az eszközkiépítési szolgáltatással való kapcsolatfelvételsel. 

A programozási eszközök újbóli kiépítésének egyik egyszerű módja az, hogy beprogramozza az eszközt, hogy kapcsolatba lépjen a létesítési szolgáltatással, hogy átmenjen a létesítési folyamaton, ha az eszköz "jogosulatlan" hibaüzenetet kap az IoT hubhoz való csatlakozás során.

Egy másik módja az, hogy mind a régi, mind az új tanúsítványok egy rövid átfedésre érvényesek legyenek, és az IoT hub használatával parancsot küldhetnek az eszközöknek, hogy újra regisztrálhassák őket a létesítési szolgáltatáson keresztül az IoT Hub-kapcsolati adataik frissítéséhez. Mivel minden eszköz másképp tudja feldolgozni a parancsokat, be kell programoznia az eszközt, hogy tudja, mi a teendő a parancs meghívásakor. Számos módon parancsolhatja az eszközt az IoT Hubon keresztül, és javasoljuk, hogy [közvetlen módszerek](../iot-hub/iot-hub-devguide-direct-methods.md) vagy [feladatok](../iot-hub/iot-hub-devguide-jobs.md) használatával kezdeményezze a folyamatot.

Az újraépítés befejezése után az eszközök az új tanúsítványaik használatával csatlakozhatnak az IoT Hubhoz.


## <a name="blacklist-certificates"></a>Feketelista-tanúsítványok

A biztonsági rés miatt előfordulhat, hogy feketelistára kell tennie egy eszköztanúsítványt. Eszköztanúsítvány feketelistára tételéhez tiltsa le a céleszköz/tanúsítvány regisztrációs bejegyzését. További információt az Eszközök lesiktatása a [Disenrollment cikk Kezelése](how-to-revoke-device-access-portal.md) című cikkben talál.

Ha egy tanúsítvány szerepel egy letiltott beléptetési bejegyzés részeként, az IoT-központtal a tanúsítványok használatával történő regisztrációra tett kísérletek sikertelenek lesznek, még akkor is, ha egy másik beléptetési bejegyzés részeként engedélyezve van.
 



## <a name="next-steps"></a>További lépések

- Ha többet szeretne tudni az Eszközkiépítési szolgáltatás X.509 tanúsítványairól, olvassa el a [Biztonság](concepts-security.md) 
- Az X.509-es hitelesítésszolgáltatói tanúsítványok igazolásának az Azure IoT Hub-eszközkiépítési szolgáltatással történő biztosításáról a [Tanúsítványok ellenőrzése című](how-to-verify-certificates.md) témakörben olvashat.
- Ha többet szeretne tudni arról, hogy miként hozhat létre a portált egy regisztrációs csoport létrehozásához, olvassa [el az Eszközregisztrációk kezelése az Azure Portalon című témakört.](how-to-manage-enrollments.md)










