---
title: Hogyan szeretné visszaállítani az X.509-tanúsítványok az Azure IoT Hub Device Provisioning Service |} A Microsoft Docs
description: Hogyan szeretné visszaállítani a Device Provisioning service-példányt az X.509-tanúsítványok
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8cf5f262a758efe08ad73e2d8066ad4b736e76d1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247721"
---
# <a name="how-to-roll-x509-device-certificates"></a>Hogyan szeretné visszaállítani az X.509 tanúsítványok

Az IoT-megoldás élettartama során kell visszaállítani a tanúsítványokat. Két fő oka a működés közbeni tanúsítványok lenne a biztonsági incidensek és a tanúsítvány lejárhat. 

Biztonsági szempontból ajánlott a rendszer biztonságának megsértése esetén a működés közbeni tanúsítványok. Részeként [tegyük fel, illetéktelen behatolás módszertan](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft szakemberekkel, reaktív jellegű biztonsági folyamatok kellene helyen költségének kiszámítására együtt van szükség. A működés közbeni az eszköztanúsítványok biztonsági folyamat része lehet. A vállalati biztonsági igényeket, hogy a megoldás a gyakoriságot, amelyben a tanúsítvány forgassa függ. Szigorúan bizalmas adatokhoz is érintő megoldások rendelkező ügyfelek bevezetése tanúsítvány naponta, míg mások állítja a tanúsítványok minden néhány évben.

A működés közbeni eszköztanúsítványok magában foglalja az eszközön, és az IoT hubon tárolt tanúsítvány frissítése. Ezt követően az eszköz segítségével építse ki újra a magát az IoT hub használata szokásos [automatikus kiépítés](concepts-auto-provisioning.md) a Device Provisioning Service szolgáltatással.


## <a name="obtain-new-certificates"></a>Új tanúsítvány beszerzése

Számos módon az IoT-eszközök új tanúsítványokat szerezzenek be. Ezek közé tartozik a tanúsítványok beszerzése az eszköz gyári, a saját tanúsítványok létrehozásához, és hozzon létre egy külső gyártótól származó tanúsítvány létrehozása, kezelése. 

Tanúsítványok alá van írva egymással egy, a legfelső szintű Hitelesítésszolgáltatói tanúsítvány megbízhatósági láncában alkotnak egy [levéltanúsítványt](concepts-security.md#end-entity-leaf-certificate). Aláíró tanúsítvány az a megbízhatósági lánc végén levéltanúsítványt aláírásához használt tanúsítvány. Aláíró tanúsítvány egy legfelső szintű Hitelesítésszolgáltatói tanúsítványt, és a egy közbenső tanúsítvány megbízhatósági láncában is lehetnek. További információkért lásd: [X.509 tanúsítványok](concepts-security.md#x509-certificates).
 
Aláíró tanúsítvány beszerzése két különböző módja van. Az első lehetőség, amelyhez használata javasolt éles rendszerek esetén, az aláíró tanúsítvány legfelső szintű hitelesítésszolgáltatótól (CA) vásárolhat. Ezzel a módszerrel kapcsolódik a biztonsági le egy megbízható forrásból. 

A második módja, ha saját X.509-tanúsítványokat OpenSSL hasonló eszköz használatával. Ez a megközelítés kiválóan alkalmazható az X.509-tanúsítványokat tesztelése, de néhány, körül biztonsági garanciákat nyújt. Azt javasoljuk, hogy csak ezt a módszert használja, ha a saját hitelesítésszolgáltató-szolgáltató segítségével előkészített tesztelési.
 

## <a name="roll-the-certificate-on-the-device"></a>A tanúsítvány visszaállítani az eszközt

Tanúsítványokat az eszközön mindig például biztonságos helyen kell tárolni egy [hardveres biztonsági modul (HSM)](concepts-device.md#hardware-security-module). A bevezetés során fokozatosan eszköztanúsítványok módja hogyan létrehozott és az eszközök az elsőként telepített függ. 

Ha a tanúsítványok kapott egy harmadik féltől származó, meg kell megvizsgáljuk hogyan állítja, a tanúsítványaikat. A folyamat szerepelni fog a megállapodás velük, vagy lehet egy külön szolgáltatás kínálnak. 

Ha a saját tanúsítványok felügyeli, kell a saját folyamatát, a tanúsítványok frissítése. Ellenőrizze, hogy mindkét korábbi és új levél tanúsítványának rendelkeznie kell az azonos köznapi név (CN). Az azonos CN azzal az eszköz is építse ki újra a magát egy ismétlődő regisztrációs bejegyzés létrehozása nélkül. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>A tanúsítvány állítsa az IoT hubban

Az eszköz tanúsítványát manuálisan felveheti egy IoT hubra. A tanúsítvány Device Provisioning service-példány használatával is automatizálható. Ebben a cikkben fogja feltételezzük a Device Provisioning service-példány automatikus kiépítés támogatásához használja.

Amikor egy eszköz kezdetben kiépítése keresztül az Automatikus kiépítés, a rendszerindítás-fel, és kapcsolatba lép a kiépítési szolgáltatás. A kiépítési szolgáltatás által az IoT hub, az eszköz levél tanúsítványt használ, a hitelesítő adatokat az eszközidentitás létrehozása előtt egy identitás-ellenőrzést végrehajtó válaszol. A kiépítési szolgáltatás majd arra utasítja az eszközt, mely az IoT hub hozzá van rendelve, és az eszköz ezután használja a levéltanúsítvány hitelesítéséhez és csatlakoztatása az IoT hubhoz. 

Miután egy új levél tanúsítványt az eszközre lett állítva, hogy már nem kapcsolódni az IoT hub mert csatlakozni egy új tanúsítványt használ. Az IoT hub csak felismeri a régi tanúsítványt az eszközre. Az eszköz kapcsolódási kísérlet eredménye "nem engedélyezett" kapcsolati hiba lesz. Ez a hiba elhárításához frissítenie kell az eszközt, hogy figyelembe vegye az eszköz új levél tanúsítvány beléptetési bejegyzésében. Ezután a kiépítési szolgáltatás frissítheti az IoT Hub eszköz beállításjegyzék-információk szerint van szükség, amikor az eszköz van-e kiépíteni. 

Az ilyen csatlakozási hibát egyetlen lehetséges kivétel lenne egy olyan forgatókönyvet, ahol létrehozta egy [regisztrációs csoportot](concepts-service.md#enrollment-group) az eszközt a kiépítési szolgáltatáshoz. Ebben az esetben ha a tanúsítvány megbízhatósági láncában az eszköz nem működés közbeni a legfelső szintű vagy köztes tanúsítványt, majd az eszköz felismer Ha az új tanúsítványt a regisztrációs csoport meghatározott megbízhatósági lánc része. Ebben a forgatókönyvben a biztonsági incidensek adott válaszként merül fel, ha legalább az adott eszköz tanúsítványokat a csoportban kell megsértették tekintendő tiltólistára kell. További információkért lásd: [adott eszközökre egy regisztrációs csoportnak tiltólistára](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

A tételek frissítése a listákban összesített tanúsítványok történik a **beléptetések kezelése** lapot. Az oldal eléréséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT Hub Device Provisioning Service-példányhoz, amelyen az eszköz beléptetési bejegyzésében.

2. Kattintson a **Regisztrációk kezelése** elemre.

    ![Regisztrációk kezelése](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Hogyan kezeli a regisztrációs bejegyzés frissítése függ, hogy egyéni regisztrációk vagy csoportos beléptetések használ. Is az ajánlott eljárások különböznek attól függően, hogy is elérhetőek tanúsítványokat a biztonsági szabályzat megsértése vagy a tanúsítvány lejárta miatt. A következő szakaszok ismertetik, hogyan kezelje ezeket a frissítéseket.


## <a name="individual-enrollments-and-security-breaches"></a>Egyéni regisztrációk és biztonsági résekkel szemben

Ha tanúsítványokat a biztonsági incidensek válaszul is elérhetőek, a következő módon, hogy azonnal törli az aktuális tanúsítványt kell használnia:

1. Kattintson a **egyéni regisztrációk**, majd kattintson a regisztrációs azonosító bejegyzést a listában. 

2. Kattintson a **Delete jelenlegi tanúsítvány** gombra, majd kattintson a mappa ikonra, válassza ki az új regisztrációs bejegyzés feltöltött tanúsítványt. Kattintson a **mentése** befejezésekor.

    Ezeket a lépéseket kell elvégezni a az elsődleges és másodlagos tanúsítvány, ha mindkét integritása sérül.

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. A feltört tanúsítvány el lett távolítva a kiépítési szolgáltatást, miután a tanúsítvány továbbra is használható, amennyiben létezik egy eszközregisztráció, az IoT hub eszköz kapcsolatok létrehozására. Meg lehet oldani ezt két módon: 

    Az első módszer lehet manuálisan keresse meg az IoT hubhoz, és azonnal törli az eszköz regisztrációját, a feltört tanúsítványhoz. Majd az eszköz újra egy frissített tanúsítvánnyal látja el, amikor létrejön egy új eszköz regisztrálása.     

    ![IoT hub-eszköz regisztrációjának törlése](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módja lehet a reprovisioning támogatási használatával építse ki újra ugyanazt az IoT hubon az eszközön. Ez a módszer használható cserélje le a tanúsítványt az eszköz regisztrációját, és az IoT hub számára. További információkért lásd: [építenie az eszközök hogyan](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Egyéni regisztrációk és a tanúsítvány lejárata

Ha a tanúsítványok tanúsítvány lejárhat kezelésére is elérhetőek, kell használnia a másodlagos tanúsítvány konfigurálását a következő csökkenthető a leállások eszközök kiépítése próbál.

Később esetén a másodlagos is tanúsítvány lejár, és konfigurálni kell, elforgathat az elsődleges konfigurációt használja. Elforgatás között az elsődleges és másodlagos tanúsítványát, így csökken az állásidő a megfelelő eszközök kiépítése.


1. Kattintson a **egyéni regisztrációk**, majd kattintson a regisztrációs azonosító bejegyzést a listában. 

2. Kattintson a **másodlagos tanúsítvány** majd kattintson a mappa ikonra, válassza ki az új regisztrációs bejegyzés feltöltött tanúsítványt. Kattintson a **Save** (Mentés) gombra.

    ![Másodlagos tanúsítvány használatával egyéni regisztrációk kezelése](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Később az elsődleges tanúsítvány lejárt, térjen vissza, és törölje az elsődleges tanúsítvány kattintva a **Delete jelenlegi tanúsítvány** gombra.

## <a name="enrollment-groups-and-security-breaches"></a>Regisztrációs csoportok és a biztonsági szabályok megsértése esetén alkalmazandók.

Biztonsági incidensek válaszul egy csoportos regisztráció frissítése, használjon, amely törli a jelenlegi gyökér szintű vagy köztes tanúsítványt közvetlenül a következő megközelítések egyikét.

#### <a name="update-compromised-root-ca-certificates"></a>Sérült biztonságú legfelső szintű Hitelesítésszolgáltatói tanúsítványok frissítése

1. Kattintson a **tanúsítványok** lapon találja a Device Provisioning service-példányt.

2. Kattintson a listában a feltört tanúsítványra, és kattintson a **törlése** gombra. A törlés megerősítéséhez írja be a tanúsítvány nevét, és kattintson a **OK**. Ismételje meg ezt a folyamatot minden veszélyeztetett tanúsítványokkal.

    ![Legfelső szintű hitelesítésszolgáltató-tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)

3. Kövesse az ismertetett lépéseket [konfigurálása ellenőrzött Hitelesítésszolgáltatói tanúsítványok](how-to-verify-certificates.md) hozzáadásának és hitelesítésének új legfelső szintű Hitelesítésszolgáltatói tanúsítványokat.

4. Kattintson a **beléptetések kezelése** a Device Provisioning service-példányt a lapra, majd kattintson a **regisztrációs csoportok** listája. Kattintson a regisztrációs csoport nevét a listában.

5. Kattintson a **Hitelesítésszolgáltatói tanúsítvány**, és válassza ki az új legfelső szintű Hitelesítésszolgáltatói tanúsítvány. Ezután kattintson a **Save** (Mentés) gombra. 

    ![Válassza ki az új legfelső szintű Hitelesítésszolgáltatói tanúsítvány](./media/how-to-roll-certificates/select-new-root-cert.png)

6. A feltört tanúsítvány el lett távolítva a kiépítési szolgáltatást, miután a tanúsítvány továbbra is használható, hogy az IoT hub eszköz kapcsolatokat, amíg az eszköz regisztrálása, létezik. Meg lehet oldani ezt két módon: 

    Az első módszer lehet manuálisan keresse meg az IoT hubhoz, és azonnal törli az eszköz regisztrációját, a feltört tanúsítványhoz. Ezután az eszközök újra üzembe a frissített tanúsítványokkal, egy új eszköz regisztrálása az minden egyes létrejön.     

    ![IoT hub-eszköz regisztrációjának törlése](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módszer reprovisioning támogatási használatával építse ki újra ugyanazt az IoT hubon az eszközök lenne. Ez a megközelítés cserélje le a tanúsítványokat az eszköz regisztrálása az IoT hub is használható. További információkért lásd: [építenie az eszközök hogyan](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Sérült biztonságú köztes tanúsítványok frissítése

1. Kattintson a **regisztrációs csoportok**, majd kattintson a csoport nevét a listában. 

2. Kattintson a **köztes tanúsítványt**, és **Delete jelenlegi tanúsítvány**. Kattintson a mappa ikonra kattintva nyissa meg az új köztes tanúsítványt kell feltölteni a regisztrációs csoport számára. Kattintson a **mentése** befejezésekor. Ezeket a lépéseket kell elvégezni a mind az elsődleges és másodlagos tanúsítvány, ha mindkét integritása sérül.

    Új köztes tanúsítvány által ellenőrzött Hitelesítésszolgáltatói főtanúsítványhoz, amely már korábban be kiépítési szolgáltatás alá kell írni. További információkért lásd: [X.509 tanúsítványok](concepts-security.md#x509-certificates).

    ![Egyéni regisztrációk kezelése](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. A feltört tanúsítvány el lett távolítva a kiépítési szolgáltatást, miután a tanúsítvány továbbra is használható, hogy az IoT hub eszköz kapcsolatokat, amíg az eszköz regisztrálása, létezik. Meg lehet oldani ezt két módon: 

    Az első módszer lehet manuálisan keresse meg az IoT hubhoz, és azonnal törli az eszköz regisztrációját, a feltört tanúsítványhoz. Ezután az eszközök újra üzembe a frissített tanúsítványokkal, egy új eszköz regisztrálása az minden egyes létrejön.     

    ![IoT hub-eszköz regisztrációjának törlése](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    A második módszer reprovisioning támogatási használatával építse ki újra ugyanazt az IoT hubon az eszközök lenne. Ez a megközelítés cserélje le a tanúsítványokat az eszköz regisztrálása az IoT hub is használható. További információkért lásd: [építenie az eszközök hogyan](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Regisztrációs csoportok és a tanúsítvány lejárata

Ha a tanúsítványok kezeléséhez tanúsítványok lejárata működés közbeni, használjon a másodlagos tanúsítvány konfigurálását módon annak biztosítása érdekében a megfelelő eszközök kiépítése állásidő nélkül.

Később esetén a másodlagos is tanúsítvány lejár, és konfigurálni kell, elforgathat az elsődleges konfigurációt használja. Elforgatás között az elsődleges és másodlagos tanúsítványát, így biztosítja, hogy a megfelelő eszközök kiépítése állásidő nélkül. 

#### <a name="update-expiring-root-ca-certificates"></a>Lejáró legfelső szintű Hitelesítésszolgáltatói tanúsítványok frissítése

1. Kövesse az ismertetett lépéseket [konfigurálása ellenőrzött Hitelesítésszolgáltatói tanúsítványok](how-to-verify-certificates.md) hozzáadásának és hitelesítésének új legfelső szintű Hitelesítésszolgáltatói tanúsítványokat.

2. Kattintson a **beléptetések kezelése** a Device Provisioning service-példányt a lapra, majd kattintson a **regisztrációs csoportok** listája. Kattintson a regisztrációs csoport nevét a listában.

3. Kattintson a **Hitelesítésszolgáltatói tanúsítvány**, és válassza ki az új legfelső szintű Hitelesítésszolgáltatói tanúsítványt a **másodlagos tanúsítvány** konfigurációja. Ezután kattintson a **Save** (Mentés) gombra. 

    ![Válassza ki az új legfelső szintű Hitelesítésszolgáltatói tanúsítvány](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Később amikor elsődleges tanúsítvány lejárt, kattintson a **tanúsítványok** lapon találja a Device Provisioning service-példányt. Kattintson a listában a lejárt tanúsítvány, és kattintson a **törlése** gombra. A törlés megerősítéséhez írja be a tanúsítvány nevét, és kattintson a **OK**.

    ![Legfelső szintű hitelesítésszolgáltató-tanúsítvány törlése](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Lejáró köztes tanúsítványok frissítése


1. Kattintson a **regisztrációs csoportok**, majd kattintson a csoport nevét a listában. 

2. Kattintson a **másodlagos tanúsítvány** majd kattintson a mappa ikonra, válassza ki az új regisztrációs bejegyzés feltöltött tanúsítványt. Kattintson a **Save** (Mentés) gombra.

    Új köztes tanúsítvány által ellenőrzött Hitelesítésszolgáltatói főtanúsítványhoz, amely már korábban be kiépítési szolgáltatás alá kell írni. További információkért lásd: [X.509 tanúsítványok](concepts-security.md#x509-certificates).

   ![Másodlagos tanúsítvány használatával egyéni regisztrációk kezelése](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Később az elsődleges tanúsítvány lejárt, térjen vissza, és törölje az elsődleges tanúsítvány kattintva a **Delete jelenlegi tanúsítvány** gombra.


## <a name="reprovision-the-device"></a>Építse ki újra az eszközt

Miután a tanúsítvány a Device Provisioning Service és az eszközhöz be lesz állítva, az eszköz is építse ki újra a saját maga kapcsolatba lép a Device Provisioning service. 

Egy könnyedén programozási eszközöket építenie az, hogy kapcsolatba kell áthaladnia az üzembe helyezési folyamatban, ha az eszköz "nem engedélyezett" hibaüzenetet kap szeretne csatlakozni az IoT hub eszközkiépítési szolgáltatás az eszközt.

Egy másik módja a régi és az új tanúsítványok rövid átfedés érvényes, és az IoT hub használatával parancsot küldhet eszközökre azok regisztrálja újra a regisztrációs szolgáltatás keresztül azok az IoT Hub kapcsolati adatok frissítése. Minden egyes eszköz eltérően parancsok képes feldolgozni, mert el tudják, mi a teendő, ha a parancs meghívása az eszközt a program. Többféleképpen is parancsot a az eszközt az IoT Hub-n keresztül, és azt javasoljuk, [közvetlen metódusok](../iot-hub/iot-hub-devguide-direct-methods.md) vagy [feladatok](../iot-hub/iot-hub-devguide-jobs.md) , elindítja a folyamatot.

Reprovisioning befejeződése után tud csatlakozni az IoT Hub, az új tanúsítványok használatával fogja eszközöket.


## <a name="blacklist-certificates"></a>Blacklist tanúsítványok

Biztonsági incidensek válaszul szükség lehet egy eszköz tanúsítványt tiltólistára kell helyezni. Egy eszköz tanúsítványt tiltólistára, tiltsa le a célként megadott eszköz vagy tanúsítvány regisztrációs bejegyzés. További információkért lásd: az eszközök áruházat a [kezelés regisztráció megszüntetésének](how-to-revoke-device-access-portal.md) cikk.

Ha egy letiltott regisztrációs bejegyzés, Regisztrálás az IoT hub használatával tett bármilyen kísérlet része, amely a tanúsítványok sikertelen lesz, még akkor is, ha engedélyezve van egy másik regisztrációs bejegyzés részeként lehetőség része egy tanúsítványt.
 



## <a name="next-steps"></a>További lépések

- A Device Provisioning Service az X.509-tanúsítványokkal kapcsolatos további tudnivalókért lásd: [biztonsági](concepts-security.md) 
- Hogyan teheti a koncepció-az-birtokában x.509-es Hitelesítésszolgáltatói tanúsítványok az Azure IoT Hub Device Provisioning Service szolgáltatással kapcsolatos további információkért lásd: [tanúsítványok ellenőrzése](how-to-verify-certificates.md)
- Egy regisztrációs csoport létrehozása a portál használatával kapcsolatos tudnivalókért lásd: [az Azure portal-eszközök beléptetésének kezelése](how-to-manage-enrollments.md).










