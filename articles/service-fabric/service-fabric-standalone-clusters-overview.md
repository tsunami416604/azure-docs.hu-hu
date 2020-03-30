---
title: Önálló service Fabric-fürtök – áttekintés
description: A Service Fabric-fürtök Windows Server és Linux rendszeren futnak, ami azt jelenti, hogy bárhol telepítheti és üzemeltetheti a Service Fabric-alkalmazásokat, ahol windows Server vagy Linux futhat.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465643"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>A Service Fabric önálló fürtjainak áttekintése

A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. A fürt részét képezi gépet vagy virtuális gépet fürtcsomópontnak nevezzük. A fürtök több ezer csomópontra méretezhetők. Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újraegyensúlyozza a szolgáltatáspartíció-replikákat és példányokat a megnövekedett számú csomópont között. Az alkalmazások általános teljesítménye javul, és csökken a memóriahoz való hozzáférés rekedése. Ha a fürt csomópontjait nem használja hatékonyan, csökkentheti a csomópontszámát a fürtben. A Service Fabric újra egyensúlyba hozza a partícióreplikákat és példányokat a csomópontok számának csökkenésével, hogy jobban kihasználja a hardvert az egyes csomópontokon.

A csomóponttípus határozza meg a fürt csomópontkészletének méretét, számát és tulajdonságait. Ezután mindegyik csomóponttípus egymástól függetlenül skálázható vertikálisan le vagy fel, eltérő nyitott portokkal rendelkezhet, és eltérő kapacitásmetrikái lehetnek. A csomóponttípusok a fürtcsomópontcsoportok szerepkörének (például „előtér” vagy „háttér”) meghatározására szolgálnak. A fürt több csomóponttípussal is rendelkezhet, de éles fürtök esetében az elsődleges csomóponttípusnak legalább öt (vagy tesztfürtök esetében legalább három) virtuális géppel kell rendelkeznie. [A Service Fabric-rendszerszolgáltatások](service-fabric-technical-overview.md#system-services) elhelyezése az elsődleges csomóponttípusra történik.

A helyszíni Service Fabric-fürt létrehozásának folyamata hasonló ahhoz a folyamathoz, amelyen a fürt bármely tetszőleges felhőben, virtuális gépek készletével jön létre. A virtuális gépek kiépítésének kezdeti lépéseit a felhőszolgáltató vagy a használt helyszíni környezet szabályozza. Miután rendelkezik egy sor virtuális gépek hálózati kapcsolat engedélyezve van közöttük, majd a lépéseket, hogy hozzanak létre a Service Fabric csomag, szerkessze a fürt beállításait, és futtassa a fürt létrehozása és felügyeleti parancsfájlok azonosak. Ez biztosítja, hogy a Service Fabric-fürtök üzemeltetésével és kezelésével kapcsolatos ismeretei és tapasztalata átruházható legyen, ha új üzemeltetési környezeteket céloz meg.

## <a name="cluster-security"></a>Fürtbiztonság
A Service Fabric-fürt egy saját erőforrás.  Az Ön felelőssége, hogy biztosítsa a fürtök, hogy segítsen megakadályozni illetéktelen felhasználók számára való csatlakozást. A biztonságos fürt különösen fontos, ha éles számítási feladatokat futtat a fürtön.

### <a name="node-to-node-security"></a>Csomópont-csomópont biztonság
Csomópont-csomópont biztonság biztosítja a kommunikációt a virtuális gépek vagy a fürt számítógépei között. Ez a biztonsági forgatókönyv biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek vehessenek részt a fürtben lévő alkalmazások és szolgáltatások üzemeltetésében. A Service Fabric X.509-es tanúsítványokat használ a fürt védelméhez és az alkalmazás biztonsági szolgáltatásainak biztosításához.  Fürttanúsítvány szükséges a fürtforgalom védelméhez, valamint a fürt- és kiszolgálóhitelesítés biztosításához.  Saját aláírású tanúsítványok használhatók tesztfürtökhöz, de egy megbízható hitelesítésszolgáltató tanúsítványát kell használni az éles fürtök védelméhez.

A Windows biztonsága a Windows önálló fürtjein is engedélyezhető. Ha Windows Server 2012 R2 és Windows Active Directory rendszert használ, javasoljuk, hogy a Windows biztonságát csoportfelügyelt szolgáltatásfiókokkal használja. Ellenkező esetben használja a Windows biztonságát a Windows-fiókokkal.

További információ: [Csomópont-csomópont biztonság](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonság
Az ügyfél-csomópont biztonság hitelesíti az ügyfeleket, és segít a fürt ügyfél- és egyéni csomópontjai közötti kommunikáció biztosításában. Ez a fajta biztonság segít biztosítani, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz és a fürtön telepített alkalmazásokhoz. Az ügyfelek egyedi azonosítása az X.509 tanúsítvány biztonsági hitelesítő adataival. Tetszőleges számú választható ügyféltanúsítvány használható a rendszergazdai vagy felhasználói ügyfelek fürttel való hitelesítésére.

Az ügyféltanúsítványok mellett az Azure Active Directory is konfigurálható az ügyfelek hitelesítésére a fürtön.

További információ: [Ügyfél-csomópont biztonság](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A Service Fabric is támogatja a hozzáférés-vezérlést, hogy korlátozza a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára. Ez segít a fürt biztonságosabbá tétele érdekében. A fürthöz csatlakozó ügyfelek számára két hozzáférés-vezérlési típus támogatott: rendszergazdai szerepkör és felhasználói szerepkör.  

További információért olvassa el [a szerepköralapú hozzáférés-vezérlés (RBAC) című.](service-fabric-cluster-security.md#role-based-access-control-rbac)

## <a name="scaling"></a>Méretezés

Az alkalmazás igények idővel változnak. Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen a megnövekedett alkalmazásterhelésnek vagy hálózati forgalomnak, vagy csökkentse a fürterőforrásait, ha az igény csökken. A Service Fabric-fürt létrehozása után vízszintesen (módosíthatja a csomópontok számát) vagy függőlegesen skálázhatja a fürtöt (módosíthatja a csomópontok erőforrásait). A fürt bármikor méretezhető, még akkor is, ha a számítási feladatok futnak a fürtön. A fürt méretezése, az alkalmazások automatikusan skálázható is.

További információért olvassa el [az önálló fürtök méretezése](service-fabric-cluster-scaling-standalone.md)című olvasni való méretezése című.

## <a name="upgrading"></a>Frissítés

Az önálló fürt olyan erőforrás, amely teljes egészében a tulajdonában van. Ön felelős az alapul szolgáló operációs rendszer javításáért és a háló frissítésekének megküldéséért. Beállíthatja, hogy a fürt automatikus futásidejű frissítéseket kapjon, amikor a Microsoft új verziót ad ki, vagy kiválaszthatja a kívánt támogatott futásidejű verziót. A hálófrissítések mellett az operációs rendszert is javíthatja, és frissítheti a fürtkonfigurációt, például a tanúsítványokat vagy az alkalmazásportokat. 

További információ: [Önálló fürtök frissítése](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Fürtöket hozhat létre az operációs rendszereket futtató virtuális gépeken vagy számítógépeken (a Linux még nem támogatott):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>További lépések
További információ az önálló fürtök [biztonságossá,](service-fabric-cluster-security.md) [méretezéséről](service-fabric-cluster-scaling-standalone.md)és [frissítéséről.](service-fabric-cluster-upgrade-standalone.md)

További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)
