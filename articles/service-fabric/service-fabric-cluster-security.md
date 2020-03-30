---
title: Azure Service Fabric-fürt biztonságossá tétele
description: Ismerje meg az Azure Service Fabric-fürt biztonsági forgatókönyveit, valamint a megvalósításukhoz használható különböző technológiákat.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: 92d2c4d03075eaafce039f94b4f03c0791985b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258680"
---
# <a name="service-fabric-cluster-security-scenarios"></a>A Service Fabric fürtjének biztonsági forgatókönyvei

Az Azure Service Fabric-fürt egy saját erőforrás. Az Ön felelőssége, hogy biztosítsa a fürtök, hogy segítsen megakadályozni illetéktelen felhasználók számára való csatlakozást. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön. Nem biztonságos fürt is létrehozható, azonban ha a fürt felügyeleti végpontokat ad a nyilvános internetnek, névtelen felhasználók csatlakozhatnak hozzá. A nem biztonságos fürtök nem támogatottak az éles számítási feladatokhoz. 

Ez a cikk az Azure-fürtök és önálló fürtök biztonsági forgatókönyveinek áttekintését, valamint a megvalósításukhoz használható különböző technológiákat ismerteti:

* Csomópont-csomópont biztonság
* Ügyfél-csomópont biztonság
* Szerepköralapú hozzáférés-vezérlés (RBAC)

## <a name="node-to-node-security"></a>Csomópont-csomópont biztonság

Csomópont-csomópont biztonság segít a virtuális gépek vagy a fürt számítógépei közötti kommunikáció biztosításában. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében.

![Csomópont-csomópont kommunikáció diagramja][Node-to-Node]

Az Azure-on futó fürtök és a Windows rendszeren futó önálló fürtök egyaránt használhatják a [tanúsítványok biztonságát](https://msdn.microsoft.com/library/ff649801.aspx) vagy a Windows Server számítógépek Windows Server rendszerének [biztonságát.](https://msdn.microsoft.com/library/ff649396.aspx)

### <a name="node-to-node-certificate-security"></a>Csomópont-csomópont tanúsítvány biztonsága

A Service Fabric x.509-es kiszolgálói tanúsítványokat használ, amelyeket fürt létrehozásakor a csomóponttípusú konfiguráció részeként ad meg. A cikk végén rövid áttekintést kaphat arról, hogy mik ezek a tanúsítványok, és hogyan szerezheti be vagy hozhatja létre őket.

Állítsa be a tanúsítványbiztonság, amikor létrehozza a fürtöt, akár az Azure Portalon, egy Azure Resource Manager sablon használatával, vagy egy önálló JSON-sablon használatával. A Service Fabric SDK alapértelmezett viselkedése a tanúsítvány telepítése és telepítése a legtávolabbi a jövőbeli lejáró tanúsítványba; a klasszikus viselkedés lehetővé tette az elsődleges és másodlagos tanúsítványok meghatározását, lehetővé téve a manuálisan kezdeményezett rollovereket, és nem ajánlott az új funkciókhoz. A használni kívánt elsődleges tanúsítványok a legtávolabbi a jövőbeli lejárati dátum, meg kell különböznie a rendszergazdai ügyfél és az írásvédett ügyfél tanúsítványokat, hogy beállított [ügyfél-csomópont biztonság](#client-to-node-security).

A tanúsítványbiztonság beállításáról az Azure-beli fürtben a [Fürt beállítása Azure Resource Manager-sablon használatával (Fürt beállítása Azure Resource Manager-sablon használatával)](service-fabric-cluster-creation-via-arm.md)témakörben olvashat.

Ha tudni szeretné, hogy miként állíthatja be a tanúsítványok biztonságát egy fürtben önálló Windows Server-fürthöz, olvassa el az Önálló fürt biztonságossá tétele A Windows rendszeren [X.509-es tanúsítványokkal (Biztonságossá) csoportonkénti létrehozása X.509-es tanúsítványokkal.](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Csomópont-csomópont Windows biztonság

A Windows biztonságának önálló Windows Server-fürthöz való beállításáról a Windows biztonsága a [Windows biztonsága szolgáltatással (Biztonságosfürt a Windows rendszeren) (Biztonságosfürt a Windows rendszeren) (Biztonságossá)](service-fabric-windows-cluster-windows-security.md)csoportban .

## <a name="client-to-node-security"></a>Ügyfél-csomópont biztonság

Az ügyfél-csomópont biztonság hitelesíti az ügyfeleket, és segít a fürt ügyfél- és egyéni csomópontjai közötti kommunikáció biztosításában. Ez a fajta biztonság segít biztosítani, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön telepített alkalmazásokhoz. Az ügyfelek egyedi azonosítása a Windows biztonsági hitelesítő adatai vagy a tanúsítvány biztonsági hitelesítő adatai alapján.

![Ügyfél-csomópont közötti kommunikáció diagramja][Client-to-Node]

Az Azure-on futó fürtök és a Windows rendszeren futó önálló fürtök egyaránt használhatják a [tanúsítványok biztonságát](https://msdn.microsoft.com/library/ff649801.aspx) vagy a [Windows biztonságát.](https://msdn.microsoft.com/library/ff649396.aspx)

### <a name="client-to-node-certificate-security"></a>Ügyfél-csomópont tanúsítvány biztonsága

Állítsa be az ügyfél-csomópont tanúsítvány biztonsága, amikor létrehozza a fürtöt, akár az Azure Portalon, egy Resource Manager sablon használatával, vagy egy önálló JSON-sablon használatával. A tanúsítvány létrehozásához adjon meg rendszergazdai vagy felhasználói ügyféltanúsítványt. Ajánlott eljárásként a megadott rendszergazdai ügyfél- és felhasználói ügyféltanúsítványoknak különbözniük kell a [csomópontok biztonságához](#node-to-node-security)megadott elsődleges és másodlagos tanúsítványoktól. A fürttanúsítványok ugyanolyan jogokkal rendelkeznek, mint az ügyfélrendszergazdai tanúsítványok. Ezeket azonban csak a fürt használja, a rendszergazdai felhasználók pedig biztonsági szempontból ajánlott eljárásként nem.

Azok az ügyfelek, amelyek a rendszergazdai tanúsítvánnyal csatlakoznak a fürthöz, teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez. Azok az ügyfelek, amelyek írásvédett felhasználói ügyféltanúsítvánnyal csatlakoznak a fürthöz, csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez. Ezek a tanúsítványok a cikk későbbi részében ismertetett RBAC-hoz használatosak.

A tanúsítványbiztonság beállításáról az Azure-beli fürtben a [Fürt beállítása Azure Resource Manager-sablon használatával (Fürt beállítása Azure Resource Manager-sablon használatával)](service-fabric-cluster-creation-via-arm.md)témakörben olvashat.

Ha tudni szeretné, hogy miként állíthatja be a tanúsítványok biztonságát egy fürtben önálló Windows Server-fürthöz, olvassa el az Önálló fürt biztonságossá tétele A Windows rendszeren [X.509-es tanúsítványokkal (Biztonságossá) csoportonkénti létrehozása X.509-es tanúsítványokkal.](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Ügyfél-csomópont Azure Active Directory biztonság az Azure-ban

Az Azure AD lehetővé teszi a szervezetek (más néven bérlők) az alkalmazások felhasználói hozzáférésének kezelése. Az alkalmazások webalapú bejelentkezési felhasználói felülettel és natív ügyfélszintű felhasználói felülettel rendelkező kedveekre vannak osztva. Ha még nem hozott létre bérlőt, kezdje a [Hogyan juthat be egy Azure Active Directory-bérlő beolvasása.][active-directory-howto-tenant]

A Service Fabric-fürt számos belépési pontot kínál a felügyeleti funkcióihoz, beleértve a webalapú [Service Fabric Explorert][service-fabric-visualizing-your-cluster] és a [Visual Studio-t.][service-fabric-manage-application-in-visual-studio] Ennek eredményeképpen hozzon létre két Azure AD-alkalmazást a fürthöz való hozzáférés szabályozásához, egy webalkalmazás hoz létre, és egy natív alkalmazás.

Az Azure-on futó fürtök esetében az Azure Active Directory (Azure AD) használatával is biztonságos hozzáférést biztosíthat a felügyeleti végpontokhoz. A szükséges Azure AD-összetevők létrehozásáról és a fürt létrehozásakor való feltöltésükről az [Azure AD beállítása az ügyfelek hitelesítéséhez című témakörben](service-fabric-cluster-creation-setup-aad.md)olvashat.

## <a name="security-recommendations"></a>Biztonsági javaslatok

Az Azure-ban üzemeltetett nyilvános hálózatban telepített Service Fabric-fürtök esetében az ügyfél-csomópont kölcsönös hitelesítésre vonatkozó javaslat a következő:

* Az Azure Active Directory használata az ügyfélidentitáshoz
* A http-kommunikáció kiszolgálóidentitásának és SSL-titkosításának tanúsítványa

Az Azure-ban üzemeltetett nyilvános hálózatban telepített Service Fabric-fürtök esetében a csomópontok között történő biztonság ra vonatkozó javaslat az, hogy fürttanúsítványt használjon a csomópontok hitelesítéséhez.

Önálló Windows Server-fürtök esetén, ha Windows Server 2012 R2 és Windows Active Directory rendszerrel rendelkezik, azt javasoljuk, hogy a Windows biztonságát csoportfelügyelt szolgáltatásfiókokkal használja. Ellenkező esetben használja a Windows biztonságát a Windows-fiókokkal.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A hozzáférés-vezérlés segítségével korlátozhatja a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára. Ez segít a fürt biztonságosabbá tétele érdekében. A fürthöz csatlakozó ügyfelek számára két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.

A Rendszergazda szerepkörhöz rendelt felhasználók teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez, beleértve az olvasási és írási képességeket is. A Felhasználói szerepkörhöz alapértelmezés szerint hozzárendelt felhasználók alapértelmezés szerint csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez (például lekérdezési képességekhez). Emellett alkalmazásokat és szolgáltatásokat is feloldhatnak.

A fürt létrehozásakor állítsa be a rendszergazdai és a felhasználói ügyfélszerepköröket. Szerepkörök hozzárendelése külön identitások biztosításával (például tanúsítványok vagy Azure AD használatával) minden egyes szerepkörtípushoz. Az alapértelmezett hozzáférés-vezérlési beállításokról és az alapértelmezett beállítások módosításáról a [Service Fabric-ügyfelek szerepköralapú hozzáférés-vezérlési témakörben olvashat bővebben.](service-fabric-cluster-security-roles.md)

## <a name="x509-certificates-and-service-fabric"></a>X.509 tanúsítványok és Service Fabric

Az X.509 digitális tanúsítványok általában az ügyfelek és kiszolgálók hitelesítésére szolgálnak. Az üzenetek titkosítására és digitális aláírására is használhatók. A Service Fabric X.509-es tanúsítványokat használ a fürt védelméhez és az alkalmazás biztonsági szolgáltatásainak biztosításához. Az X.509 digitális tanúsítványokról a [Tanúsítványok megtekintése](https://msdn.microsoft.com/library/ms731899.aspx)című témakörben talál további információt. A [Key Vault](../key-vault/key-vault-overview.md) segítségével kezelheti a Service Fabric-fürtök tanúsítványait az Azure-ban.

Néhány fontos dolog, hogy fontolja meg:

* Tanúsítványok létrehozásához az éles számítási feladatokat futtató fürtökhöz használjon megfelelően konfigurált Windows Server tanúsítványszolgáltatást, vagy egy jóváhagyott [hitelesítésszolgáltatótól](https://en.wikipedia.org/wiki/Certificate_authority).
* Soha ne használjon olyan ideiglenes vagy teszttanúsítványokat, amelyeket olyan eszközökkel hoz létre, mint a MakeCert.exe éles környezetben.
* Önaláírt tanúsítványt használhat, de csak tesztfürtben. Ne használjon önaláírt tanúsítványt éles környezetben.
* A tanúsítvány ujjlenyomatának létrehozásakor mindenképpen hozzon létre egy SHA1 ujjlenyomatot. Az SHA1 az ügyfél- és fürttanúsítvány ujjlenyomatának konfigurálásakor használt.

### <a name="cluster-and-server-certificate-required"></a>Fürt- és kiszolgálótanúsítvány (kötelező)

Ezek a tanúsítványok (egy elsődleges és opcionálisan másodlagos) szükségesek a fürt biztonságossá tétele és a fürthöz való jogosulatlan hozzáférés megakadályozása érdekében. Ezek a tanúsítványok biztosítják a fürt és a kiszolgáló hitelesítését.

A fürthitelesítés hitelesíti a csomópont-csomópont kommunikációt a fürtösszevonáshoz. Csak azok a csomópontok csatlakozhatnak a fürthöz, amelyek ezzel a tanúsítvánnyal igazolni tudják identitásukat. A kiszolgálóhitelesítés hitelesíti a fürtkezelési végpontokat egy felügyeleti ügyfélszámára, így a felügyeleti ügyfél tudja, hogy a valódi fürthöz beszél, és nem egy "középen lévő emberhez". Ez a tanúsítvány egy SSL-t is biztosít a HTTPS-felügyeleti API-hoz és a Service Fabric Explorer HTTPS-en keresztüli kezeléséhez. Amikor egy ügyfél vagy csomópont hitelesít egy csomópontot, az egyik kezdeti ellenőrzés a Téma mezőben lévő **köznapi** név értéke. Ennek a közönséges névnek vagy a tanúsítványok egyik tulajdonosalternatív nevének (SAN) szerepelnie kell az engedélyezett köznapi nevek listáján.

A bizonyítványnak a következő követelményeknek kell megfelelnie:

* A tanúsítványnak titkos kulcsot kell tartalmaznia. Ezek a tanúsítványok általában .pfx vagy .pem kiterjesztéssel rendelkeznek.  
* A tanúsítványt kulcscseréhez kell létrehozni, amely személyes adatcserefájlba (.pfx) exportálható.
* A **tanúsítvány tulajdonosnevének meg kell egyeznie a Service Fabric-fürt eléréséhez használt tartománnyal.** Ez az egyeztetés szükséges egy SSL a fürt HTTPS-kezelési végpont és a Service Fabric Explorer. A *.cloudapp.azure.com tartományhitelesítésszolgáltatójától nem szerezhet be SSL-tanúsítványt. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Néhány más dolog, hogy fontolja meg:

* A **Tárgy** mezőtöbb értékből is kijöhet. Minden érték egy inicializálással van előtaggal, amely jelzi az értéktípusát. Általában az inicializálás **KN** *(a köznapi név);* például **CN =\.www contoso.com**.
* A **Tárgy** mező üres lehet.
* Ha a választható **Tulajdonos alternatív név** mező ki van töltve, akkor a tanúsítvány köznapi nevével és san-onként egy bejegyzéssel kell rendelkeznie. Ezek **dns-névértékként** vannak megadva. A biztonsági tanúsítványokkal rendelkező tanúsítványok létrehozásáról a [Tulajdonos alternatív név hozzáadása biztonságos LDAP-tanúsítványhoz](https://support.microsoft.com/kb/931351)című témakörből megtudhatja.
* A tanúsítvány **Tervezett célok** mezőjének értékének tartalmaznia kell egy megfelelő értéket, például **a kiszolgáló hitelesítése** vagy **az ügyfélhitelesítés**.

### <a name="application-certificates-optional"></a>Kérelmezési tanúsítványok (nem kötelező)

Tetszőleges számú további tanúsítvány telepíthető a fürtre alkalmazásbiztonsági okokból. A fürt létrehozása előtt vegye figyelembe azokat az alkalmazásbiztonsági forgatókönyveket, amelyekhez a csomópontokra egy tanúsítványt kell telepíteni, például:

* Az alkalmazás konfigurációs értékeinek titkosítása és visszafejtése.
* Adatok titkosítása csomópontok között a replikáció során.

A biztonságos fürtök létrehozásának fogalma ugyanaz, függetlenül attól, hogy Linux vagy Windows-fürtökről van-e szó.

### <a name="client-authentication-certificates-optional"></a>Ügyfélhitelesítési tanúsítványok (nem kötelező)

A rendszergazdai vagy felhasználói ügyfélműveletekhez tetszőleges számú további tanúsítvány adható meg. Az ügyfél használhatja ezt a tanúsítványt, ha kölcsönös hitelesítésre van szükség. Az ügyféltanúsítványokat általában nem külső hitelesítésszolgáltató bocsátja ki. Ehelyett az aktuális felhasználói hely személyes tárolója általában egy legfelső szintű hitelesítésszolgáltató által ott elhelyezett ügyféltanúsítványokat tartalmaz. A tanúsítványnak **ügyfélhitelesítési célértékkel** kell **rendelkeznie.**  

Alapértelmezés szerint a fürttanúsítvány rendszergazdai ügyféljogosultságokkal rendelkezik. Ezeket a további ügyféltanúsítványokat nem szabad telepíteni a fürtbe, de a fürtkonfigurációban engedélyezettként vannak megadva.  Az ügyféltanúsítványokat azonban telepíteni kell az ügyfélgépekre a fürthöz való csatlakozáshoz és a műveletek végrehajtásához.

> [!NOTE]
> A Service Fabric-fürt összes felügyeleti műveletéhez kiszolgálói tanúsítványok ra van szükség. Az ügyféltanúsítványok nem használhatók felügyeleti célokra.

## <a name="next-steps"></a>További lépések

* [Fürt létrehozása az Azure-ban Erőforrás-kezelő sablon használatával](service-fabric-cluster-creation-via-arm.md)
* [Fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
