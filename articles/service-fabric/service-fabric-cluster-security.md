---
title: "Az Azure Service Fabric-fürt biztonságos |} Microsoft Docs"
description: "További információk a biztonsági forgatókönyvek az Azure Service Fabric-fürt és a segítségével alkalmazásukhoz szükséges különböző technológiákat."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>A Service Fabric-fürt biztonsági forgatókönyvek
Az Azure Service Fabric-fürt saját erőforrás. Biztosítania kell a fürtökhöz, hogy megakadályozza a jogosulatlan felhasználók az csatlakozik hozzájuk. A biztonságos fürt különösen fontos, ha a fürtön fut. a termelési számítási feladatokhoz. Bár lehetséges egy nem biztonságos fürtök létrehozásához, ha a fürt felügyeleti végpontok a nyilvános internethez azt mutatja, a névtelen felhasználók csatlakozhatnak azt. 

Ez a cikk a következő biztonsági forgatókönyvek Azure fürtök és önálló fürtök és a megvalósításukhoz segítségével különböző technológiák áttekintése:

* Csomópontok biztonsági
* Ügyfél-csomópont biztonsági
* Szerepköralapú hozzáférés-vezérlés (RBAC)

## <a name="node-to-node-security"></a>Csomópontok biztonsági
Csomópont-csomópont biztonsági segít a virtuális gépek vagy a számítógép egy fürtben közötti biztonságos kommunikációhoz. Ez a forgatókönyv biztosítja, hogy csak azok a számítógépek, amelyek jogosultak a fürthöz való csatlakoztatáshoz részt vehetnek-alkalmazások és szolgáltatások a fürt üzemeltetéséhez.

![Csomópontok kommunikáció ábrája][Node-to-Node]

Azure és az önálló fürtökön futó mindkét Windows rendszert futtató fürtöket választhat [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx) Windows Server számítógépek.

### <a name="node-to-node-certificate-security"></a>Csomópontok tanúsítvány biztonság
A Service Fabric X.509 kiszolgálói tanúsítványok, melyet a csomóponttípus konfiguráció részeként egy fürt használja. Ez a cikk végén Mik azok a tanúsítványok, és hogyan szerezni, vagy hozza létre a címzetteket rövid áttekintését láthatja.

Állítsa be a biztonsági tanúsítvány, Azure Resource Manager-sablonnal, vagy egy önálló JSON-sablon használatával, vagy az Azure portálon, a fürt létrehozásakor. Beállíthatja, hogy egy elsődleges tanúsítványt és egy nem kötelező másodlagos tanúsítvány tanúsítvány előgörgetések használt. Az elsődleges és másodlagos tanúsítványok, beállíthatja a felügyeleti ügyfél és a csak olvasható ügyféltanúsítványok állítja be különböznie kell [ügyfél-csomópont biztonsági](#client-to-node-security).

Tanúsítvány biztonsági fürtben beállítása az Azure-ba, lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md).

Egy különálló Windows Server-fürt fürt tanúsítvány biztonsági beállításával kapcsolatban a [Windows önálló fürtben, X.509 tanúsítvány használatával biztonságos](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Csomópont-csomópont Windows biztonsági
Windows biztonsági egy különálló Windows Server-fürt beállításával kapcsolatban a [biztonságos Windows önálló fürtben, a Windows biztonsági](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél-csomópont biztonsági hitelesíti az ügyfeleket, és segít az ügyfél és a fürt egyes csomópontjai közötti biztonságos kommunikációhoz. Az ilyen típusú biztonsági biztosíthatja, hogy csak hitelesített felhasználók férhetnek hozzá a fürtöt és a központilag telepített alkalmazások kerülnek a fürtön. Ügyfelek egyedileg azonosítja a Windows hitelesítő adatokat vagy a tanúsítvány hitelesítő adatokat.

![Ügyfél-csomópont kommunikációs ábrája][Client-to-Node]

Azure és az önálló fürtökön futó mindkét Windows rendszert futtató fürtöket választhat [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Ügyfél-csomópont tanúsítvány biztonság
Ügyfél-csomópont tanúsítvány biztonsági beállítása, a Resource Manager-sablon használatával, vagy egy önálló JSON-sablon használatával, vagy az Azure portálon, a fürt létrehozásakor. A tanúsítvány létrehozásához adjon meg egy rendszergazdai ügyféltanúsítvány vagy felhasználói ügyféltanúsítványt. Ajánlott eljárásként, az elsődleges és másodlagos tanúsítványokat állít be különböznie kell a felügyeleti ügyfél és a felhasználói ügyféltanúsítványok megadott [-csomópontok biztonsági](#node-to-node-security). Alapértelmezés szerint a fürt a csomópontok biztonság hozzáadja a tanúsítványokat engedélyezett ügyfél felügyeleti tanúsítványok listájához.

A fürt felügyeleti tanúsítvány használatával csatlakozó ügyfelek felügyeleti képességek teljes hozzáféréssel rendelkeznek. A fürt a felhasználó csak olvasható ügyféltanúsítvány használatával csatlakozó ügyfelek felügyeleti képességek csak olvasási hozzáféréssel rendelkeznek. Ezek a tanúsítványok érvényesek az RBAC azt a cikk későbbi részében leírt.

Tanúsítvány biztonsági fürtben beállítása az Azure-ba, lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md).

Egy különálló Windows Server-fürt fürt tanúsítvány biztonsági beállításával kapcsolatban a [Windows önálló fürtben, X.509 tanúsítvány használatával biztonságos](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Ügyfél-csomópont Azure Active Directory biztonsági az Azure-on
Az Azure-on futó fürtök esetén is gondoskodhat a felügyeleti végpontok elérését Azure Active Directory (Azure AD) segítségével. Megtudhatja, hogyan kell létrehozni a szükséges az Azure AD-összetevők, hogyan tölthetők fel, a fürt, és ezt követően csatlakoztatása a fürt létrehozásakor, lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Biztonsági javaslatok
Az Azure-fürtök esetén a csomópontok biztonság érdekében azt javasoljuk, hogy használja-e az Azure AD biztonsági ügyfelek és a tanúsítványok hitelesítéséhez.

Az önálló Windows Server-fürtök esetén Ha Windows Server 2012 R2 és Windows Active Directory, azt javasoljuk, hogy a csoportosan felügyelt szolgáltatásfiókok használjon Windows biztonsági. Ellenkező esetben használja a Windows biztonsági Windows fiókokhoz.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Hozzáférés-vezérlés használatával korlátozhatja az egyes különböző felhasználói csoportokhoz a fürt működését. Ezáltal biztonságosabbá teszi a fürt. Hozzáférés-vezérlési kétféle fürthöz csatlakozó ügyfelek támogatottak: rendszergazdai szerepkör és a felhasználói szerepkör.

A rendszergazda szerepkörhöz hozzárendelt felhasználók teljes hozzáférése felügyeleti képességeket, beleértve az írási és olvasási képességeit. Alapértelmezés szerint a felhasználói szerepkörhöz hozzárendelt felhasználók kezelési képességek (például lekérdezési lehetőségek) csak olvasási hozzáféréssel rendelkeznek. Akkor is oldhatja alkalmazásokhoz és szolgáltatásokhoz.

A rendszergazdai és felhasználói ügyfél szerepkörök beállítása a fürt létrehozásakor. Szerepkörök hozzárendelése megadásával külön identitások (például tanúsítványok vagy segítségével az Azure AD) az egyes szerepkör. Alapértelmezett hozzáférés-vezérlési beállításokkal és alapértelmezett beállítások módosításával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X.509-tanúsítványokat és a Service Fabric
X.509 digitális tanúsítványok általánosan használt ügyfelek és kiszolgálók hitelesítéséhez. Akkor is digitális aláírására és titkosítására. X.509 digitális tanúsítványokkal kapcsolatos további információkért lásd: [tanúsítványok használata](http://msdn.microsoft.com/library/ms731899.aspx).

Fontos szempontokat kell figyelembe venni:

* Tanúsítványok termelési számítási feladatokhoz futtató fürtök létrehozásához használja a megfelelően konfigurált Windows kiszolgálói tanúsítvány szolgáltatások, vagy egy jóváhagyott közül [hitelesítésszolgáltató-tanúsítvány](https://en.wikipedia.org/wiki/Certificate_authority).
* Soha ne használja a minden ideiglenes vagy eszközökkel, például a MakeCert.exe éles környezetben létrehozott tanúsítványok tesztelése.
* Egy önaláírt tanúsítványt, de csak tesztelési fürtben használható. Éles környezetben ne használjon önaláírt tanúsítványt.

### <a name="server-x509-certificates"></a>Kiszolgáló X.509-tanúsítványokat
Kiszolgáló tanúsítványának rendelkeznie kell elsődleges feladata, a kiszolgáló (csomópont) az ügyfelek hitelesítése, vagy a kiszolgáló (csomópont) kiszolgálóhoz (csomópont) hitelesítése. Amikor egy ügyfél vagy a csomópont hitelesíti a csomópont, a kezdeti ellenőrzések egyik a köznapi nevének értékét a **tulajdonos** mező. A közös név vagy a tanúsítványok tulajdonosának alternatív Tulajdonosnevekkel (SAN) egyik engedélyezett köznapi nevek listája a jelen kell lennie.

San-okkal rendelkező tanúsítványok létrehozásához, lásd: [egy biztonságos LDAP-tanúsítványhoz a tulajdonos alternatív neve hozzáadása](http://support.microsoft.com/kb/931351).

A **tulajdonos** mezőben több érték szerepelhet. Minden egyes érték van előtagként az inicializálás érték típusát jelzi. Általában a inicializálásának **CN** (a *köznapi név*), például **CN = www.contoso.com**. A **tulajdonos** lehet, hogy a mező üres. Ha az opcionális **tulajdonos alternatív neve** mező fel van töltve, mindkét köznapi neve a tanúsítványt és egy tételt SAN kell rendelkeznie. Ezek vannak megadva: **DNS-név** értékeket.

Értékét a **kívánt célok** a tanúsítvány mezőjében tartalmaznia kell egy megfelelő értéket, mint például **kiszolgálóhitelesítés** vagy **ügyfél-hitelesítés**.

### <a name="client-x509-certificates"></a>Ügyfél X.509-tanúsítványokat
Ügyféltanúsítványok általában nem hitelesítésszolgáltató által kiállított egy harmadik fél. Ehelyett a személyes tárolóba, az aktuális felhasználó helye általában tartalmaz helyeztek szerint egy legfelső szintű hitelesítésszolgáltatóval rendelkező ügyféltanúsítványok egy **kívánt célok** értékének **ügyfél-hitelesítés**. Az ügyfél használhatja ezt a tanúsítványt, ha kölcsönös hitelesítésre szükség.

> [!NOTE]
> A Service Fabric-fürtök összes felügyeleti műveleteihez kiszolgálói tanúsítványokat igényel. Ügyféltanúsítványok felügyeleti nem használható.

## <a name="next-steps"></a>Következő lépések
* [Fürt létrehozása az Azure Resource Manager-sablon használatával](service-fabric-cluster-creation-via-arm.md) 
* [Fürt létrehozása az Azure portál használatával](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
