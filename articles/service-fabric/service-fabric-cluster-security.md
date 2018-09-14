---
title: Egy Azure Service Fabric-fürt biztonságossá tétele |} A Microsoft Docs
description: Ismerje meg a biztonsági forgatókönyvek az Azure Service Fabric-fürt és a különböző technológiák használatával végrehajtja azokat.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: aljo
ms.openlocfilehash: e791ddd3ade2ff486f1c3ec123695ecc155353d6
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541904"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric-fürtök biztonsági forgatókönyveit
Azure Service Fabric-fürt saját erőforrás. A feladata biztonságossá tétele a-fürtök segítségével megakadályozhatja a jogosulatlan felhasználókat csatlakozik hozzájuk. Biztonságos fürt különösen fontos, ha a fürtön futtatnak éles számítási feladatokat. Bár lehetséges hozhat létre nem biztonságos fürtöt, ha a fürt elérhetővé teszi a nyilvános internetre felügyeleti végpontok, névtelen felhasználók csatlakozhatnak hozzá. Nem biztonságos fürtökhöz nem támogatottak a termelési számítási feladatokhoz. 

Ez a cikk a biztonsági forgatókönyvek az Azure-fürtök és önálló fürtök és a különböző technológiák használatával végrehajtja az áttekintést:

* Csomópontok közötti biztonsági
* Ügyfél-csomópont biztonsági
* Szerepköralapú hozzáférés-vezérlés (RBAC)

## <a name="node-to-node-security"></a>Csomópontok közötti biztonsági
Csomópontok közötti security segítséget nyújt a virtuális gépek és a egy fürtben számítógépek közötti biztonságos kommunikációhoz. A biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakoztatáshoz jogosult számítógépek részt vehetnek-üzemeltető, alkalmazások és szolgáltatások a fürtben.

![Csomópontok közötti kommunikáció ábrája][Node-to-Node]

Az Azure és az önálló fürtök futó mindkét Windows rendszert futtató fürtöket bármelyiket használhatja [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx) Windows Server számítógépek.

### <a name="node-to-node-certificate-security"></a>Biztonsági csomópontok tanúsítvány
A Service Fabric-fürt létrehozásakor az a csomóponttípus konfiguráció részeként megadott X.509 kiszolgálói tanúsítványok használ. Ez a cikk végén megjelenik röviden bemutatja ezeket a tanúsítványokat is, és hogyan beszerezni, vagy hozza létre őket.

Tanúsítvány biztonsági beállítása egy Azure Resource Manager-sablon használatával, vagy egy különálló JSON-sablon használatával, vagy az Azure Portalon, a fürt létrehozásakor. Service Fabric SDK alapértelmezett viselkedését, hogy üzembe helyezése, és telepítse a tanúsítványt a legtávolabbi és a jövőbeli lejáró tanúsítványba; a klasszikus viselkedés az elsődleges és másodlagos tanúsítványát, hogy manuálisan kezdeményezett előgörgetések megadása megengedett, és a használata nem ajánlott keresztül az új funkciókat. Lesz, használja a jövőbeli lejáró dátumra legtávolabbi rendelkezik elsődleges tanúsítványokat nem egyezhet meg a rendszergazdai ügyfél és a beállított csak olvasható ügyfél-tanúsítványok az [ügyfél a csomópont közötti biztonsághoz](#client-to-node-security).

Fürt tanúsítvány biztonsági beállítása az Azure-ban kapcsolatban lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md).

Hogyan állítható be egy különálló Windows Server-fürt a fürt tanúsítványalapú biztonsággal kapcsolatban lásd: [Windows különálló fürt védelme X.509-tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Csomópontok közötti Windows biztonsági
Hogyan állítható be egy különálló Windows Server-fürt Windows biztonsági kapcsolatban lásd: [különálló fürt védelme a Windows a Windows rendszerbiztonság használatával](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél-csomópont biztonsági hitelesíti az ügyfeleket, és segít az ügyfél és a fürt egyes csomópontjain közötti biztonságos kommunikációhoz. Az ilyen típusú biztonsági segítségével biztosíthatja, hogy csak a jogosult felhasználók hozzáférését a fürt és a fürtön üzembe helyezett alkalmazások. Az ügyfelek Windows biztonsági hitelesítő adataik vagy a tanúsítvány biztonsági hitelesítő adataik egyedileg azonosítja.

![Ügyfél és a csomópont közötti kommunikáció ábrája][Client-to-Node]

Az Azure és az önálló fürtök futó mindkét Windows rendszert futtató fürtöket bármelyiket használhatja [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Ügyfél-csomópont tanúsítványalapú biztonsággal
Ügyfél-csomópont tanúsítvány biztonsági beállítása a Resource Manager-sablon használatával, vagy egy különálló JSON-sablon használatával, vagy az Azure Portalon, a fürt létrehozásakor. A tanúsítvány létrehozásához adjon meg egy rendszergazdai ügyfél tanúsítványa vagy felhasználói ügyféltanúsítványt. Ajánlott eljárásként a felügyeleti ügyfél és a felhasználói ügyféltanúsítványok, adja meg a megadott elsődleges és másodlagos tanúsítványát különböznie kell [csomópontok közötti biztonsági](#node-to-node-security). Alapértelmezés szerint a fürt csomópont és csomópont közötti biztonsághoz hozzáadja a tanúsítványokat az engedélyezett ügyfél felügyeleti tanúsítványok listájára.

A felügyeleti tanúsítvány használatával a fürthöz csatlakozó ügyfelek és a felügyeleti képességek teljes hozzáféréssel rendelkezik. Az írásvédett felhasználó ügyféltanúsítvány használatával a fürthöz csatlakozó ügyfelek csak olvasási hozzáférés-felügyeleti képességekkel rendelkezik. Ezek a tanúsítványok szolgálnak az RBAC a cikk későbbi részében leírt.

Fürt tanúsítvány biztonsági beállítása az Azure-ban kapcsolatban lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md).

Hogyan állítható be egy különálló Windows Server-fürt a fürt tanúsítványalapú biztonsággal kapcsolatban lásd: [Windows különálló fürt védelme X.509-tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Ügyfél-csomópont Azure Active Directory biztonsági az Azure-ban
Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). Alkalmazások vannak osztva a webalapú bejelentkezési felhasználói felület és a egy natív ügyfél felhasználói élményét. Ha még nem hozott létre a bérlőt, először olvassa el [Azure Active Directory-bérlő beszerzése][active-directory-howto-tenant].

Service Fabric-fürt kínál a különböző belépési pontok annak felügyeleti funkciójához, beleértve a webalapú [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] és [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Ennek eredményeképpen hozzon létre két Azure AD-alkalmazások a fürthöz való hozzáférés szabályozásához, egy webalkalmazás és a egy natív alkalmazást.

Az Azure-ban futó fürtök esetén is biztosíthatók felügyeleti végpontok elérését az Azure Active Directory (Azure AD) használatával. Megtudhatja, hogyan hozhat létre a szükséges Azure AD-összetevőket és hogyan tölthetők fel a fürt létrehozásakor: [állítsa be az Azure AD-ügyfelek hitelesítésére](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Biztonsági javaslatok
Azure fürtök esetén a csomópontok biztonság érdekében azt javasoljuk, hogy Azure AD biztonsági tanúsítványok és az ügyfelek hitelesítéséhez használ.

Önálló Windows Server-fürtök Ha rendelkezik Windows Server 2012 R2 és Windows Active Directory, javasoljuk, hogy a csoportosan felügyelt szolgáltatásfiókok használatához Windows biztonsági. Ellenkező esetben használja a Windows biztonsági Windows-fiókokkal.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Hozzáférés-vezérlés használatával férjenek hozzá az egyes fürtműveletek a különböző felhasználói csoportokhoz. Ez segít a fürt biztonságát. Hozzáférés-vezérlési kétféle fürthöz csatlakozó ügyfelek támogatottak: rendszergazdai és felhasználói szerepkört.

A rendszergazda szerepkörrel felruházott felhasználók teljes hozzáférése kezelési funkciókat, például olvasási és írási képességeket. Alapértelmezés szerint a felhasználói szerepkörhöz hozzárendelt felhasználók felügyeleti képességeket (például a lekérdezési képességek) csak olvasási hozzáféréssel rendelkeznek. Akkor is fel tudja oldani alkalmazásokat és szolgáltatásokat.

Klient rendszergazdai és felhasználói szerepkörök beállítása a fürt létrehozásakor. Szerepkörök hozzárendelése azáltal, hogy különálló (például úgy, hogy a tanúsítványok vagy Azure AD) minden egyes szerepkör-típushoz. Alapértelmezett hozzáférés-vezérlési beállításokkal és alapértelmezett beállítások módosításával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509-tanúsítványokat és a Service Fabric
X.509 digitális tanúsítványok gyakran használják az ügyfelek és kiszolgálók hitelesítéséhez. Is használhatók titkosítására és digitális aláírására. Service Fabric-fürt biztonságossá tétele és adja meg az alkalmazás biztonsági funkciók X.509-tanúsítványokat használ. X.509 digitális tanúsítványokkal kapcsolatos további információkért lásd: [-tanúsítványok használata](http://msdn.microsoft.com/library/ms731899.aspx). Használhat [Key Vault](../key-vault/key-vault-get-started.md) kattintva kezelheti az Azure Service Fabric-fürtök tanúsítványait.

Fontos szempontokat kell figyelembe venni:

* Éles számítási feladatokat futtató fürtök tanúsítványai létrehozásához használja egy megfelelően konfigurált Windows Server tanúsítvánnyal, illetve egy jóváhagyott közül [hitelesítésszolgáltatói (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
* Soha ne használja a bármely ideiglenes, vagy tesztelje éles környezetben a MakeCert.exe más eszközök használatával létrehozott tanúsítványokat.
* Használhatja egy önaláírt tanúsítványt, de csak egy teszt fürtben. Éles környezetben ne használjon önaláírt tanúsítványt.
* A tanúsítvány ujjlenyomatát létrehozásakor ügyeljen arra, hozzon létre egy SHA1-ujjlenyomatot. SHA1 a rendszer ezt használja az ügyfél és a fürt tanúsítvány-ujjlenyomatok konfigurálásakor.

### <a name="cluster-and-server-certificate-required"></a>Fürt és a kiszolgálói tanúsítványt (kötelező)
Fürt biztonságossá tétele és a jogosulatlan hozzáférés elkerülése érdekében, hogy ezeket a tanúsítványokat (egy elsődleges és opcionálisan egy másodlagos) szükségesek. Ezek a tanúsítványok fürt- és hitelesítést biztosítanak.

Fürt hitelesítési hitelesíti a fürt összevonási csomópontok közötti kommunikációt. Csak a is igazolnia az identitását, ezt a tanúsítványt a csomópontok csatlakozhassanak a fürthöz. Így az ügyfél felismeri, hogy a valódi fürtön, és nem a "közbeékelődéses" beszél, Server-hitelesítést végzi a hitelesítést a fürtkezelési végpontoknak egy kezelési ügyfél. Ezt a tanúsítványt is biztosít az SSL protokoll a HTTPS-felügyeleti API-hoz és a Service Fabric Explorert a HTTPS-kapcsolaton keresztül. Amikor egy ügyfél vagy a csomópont végzi a hitelesítést egy csomópontot, a kezdeti ellenőrzések egyik köznapi nevet értékét a **tulajdonos** mező. A köznapi név vagy a tanúsítványok tulajdonosának alternatív Tulajdonosneveket (SAN) egyik engedélyezett köznapi nevek listája a jelen kell lennie.

A tanúsítvány a következő követelményeknek kell megfelelnie:

* A tanúsítványnak tartalmaznia kell egy titkos kulcsot. Ezek a tanúsítványok általában rendelkeznek bővítmények .pfx vagy .pem  
* A tanúsítvány a kulcscsere, amely egy személyes információcsere (.pfx) fájl exportálható kell létrehozni.
* A **tanúsítvány tulajdonosnevének egyeznie kell a tartományhoz, amelyben a Service Fabric-fürt eléréséhez használt**. Erre a megfeleltetésre azért szükség az SSL protokoll biztosítja a fürt HTTPS-felügyeleti végpont és a Service Fabric Explorer. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) nem szerezheti be a *. cloudapp.Azure.com formát követi tartományhoz. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Más szempontokat kell figyelembe venni:

* A **tulajdonos** mezőben több érték is rendelkezhet. Az egyes értékek előtaggal van egy inicializálási jelzi az érték típusa. Általában a inicializálásának **CN** (a *köznapi név*); például **CN = www.contoso.com**. 
* A **tulajdonos** mező üres is lehet. 
* Ha az opcionális **tulajdonos alternatív neve** mező fel van töltve, mind a köznapi név a tanúsítvány és a egy tételt SAN kell rendelkeznie. Ezek vannak megadva: **DNS-név** értékeket. Megtudhatja, hogyan hozhat létre tanúsítványokat, amelyek San-okkal, lásd: [hozzáadása a secure LDAP-tanúsítványt a tulajdonos alternatív neve](http://support.microsoft.com/kb/931351).
* Értékét a **kívánt célok** mezőé tartalmaznia kell egy megfelelő értéket, például **kiszolgálóhitelesítés** vagy **ügyfél-hitelesítés**.

### <a name="application-certificates-optional"></a>Tanúsítványok (nem kötelező)
Tetszőleges számú további tanúsítványok telepíthető alkalmazás biztonsági okokból egy fürtön. Mielőtt létrehozná a fürtöt, vegye figyelembe a kell telepíteni a csomópontokon, például a tanúsítványt igénylő alkalmazásbiztonsági forgatókönyveket:

* Titkosítás és visszafejtés alkalmazáskonfigurációs értékeket.
* Az adatok titkosítása az csomópont közötti replikálás során.

A fürtök biztonságos az elképzelés az, azonos, hogy azok Linux vagy Windows-fürtök.

### <a name="client-authentication-certificates-optional"></a>(Nem kötelező) az ügyfél-hitelesítési tanúsítványok
Tetszőleges számú további tanúsítványok rendszergazdai vagy a felhasználói műveletek adható meg. Az ügyfél használhatja ezt a tanúsítványt, ha kölcsönös hitelesítésre szükség. Ügyféltanúsítványok általában nem állítják ki egy külső hitelesítésszolgáltató. Ehelyett a felhasználó aktuális tartózkodási személyes tanúsítványtárolóban általában az ügyféltanúsítványok, melyeket egy legfelső szintű hitelesítésszolgáltatóval tartalmazza. A tanúsítványnak rendelkeznie kell egy **kívánt célok** értékét **ügyfél-hitelesítés**.  

Alapértelmezés szerint a fürt tanúsítványt a felügyeleti ügyfél jogosultságokkal rendelkezik. További ügyfél-tanúsítványok nem telepíthető a fürtbe, de a fürt konfigurációját a megengedett vannak megadva.  Azonban az ügyféltanúsítványokat az ügyfélgépek csatlakozhat a fürthöz, és műveleteket kell telepíteni kell.

> [!NOTE]
> A Service Fabric-fürtök összes felügyeleti művelet kiszolgálói tanúsítványokra van szükségük. Ügyféltanúsítványok Management nem használható.

## <a name="next-steps"></a>További lépések
* [Fürt létrehozása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md) 
* [Fürt létrehozása az Azure portal használatával](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
