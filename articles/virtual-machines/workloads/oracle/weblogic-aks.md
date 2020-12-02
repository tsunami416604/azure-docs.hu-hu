---
title: Az Oracle WebLogic Server Azure Kubernetes szolgáltatásban való futtatásának megoldásai
description: Ismerje meg, hogyan futtathatja az Oracle WebLogic Servert az Azure Kubernetes szolgáltatásban.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: cf44ad8d81656248329d993d86e9922dc4985258
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486674"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Milyen megoldások futnak az Oracle WebLogic Server Azure Kubernetes Service-ben való futtatásához?

Ez az oldal az Oracle WebLogic Server (WLS) Azure Kubernetes szolgáltatásban (ak) való futtatásának megoldásait ismerteti. Ezeket a megoldásokat az Oracle és a Microsoft közösen fejleszti és támogatja.

A WebLogic-kiszolgálót az Azure Virtual Machines is futtathatja. Ennek a megoldásnak a leírását [ebben a Microsoft-cikkben](./oracle-weblogic.md)találja.

A WebLogic Server egy vezető Java-alkalmazáskiszolgáló, amely a legtöbb kritikus vállalati Java-alkalmazást futtatja szerte a világon. A WebLogic-kiszolgáló az Oracle Software Suite-hoz készült middleware Foundation-t képezi. Az Oracle és a Microsoft elkötelezte magát arra, hogy a WebLogic-kiszolgáló ügyfeleinek az Azure-ban vezető felhőalapú platformként futtatott számítási feladatokat és rugalmasságot biztosítanak.

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AK Certified és supported
A WebLogic-kiszolgálót az Oracle és a Microsoft is tanúsítja, hogy jól fusson az AK-on. Az AK-alapú megoldások WebLogic-kiszolgálója úgy van kialakítva, hogy a lehető legkönnyebben fusson a tárolók és a felkészített Java EE-alkalmazások a Docker és a Kubernetes-infrastruktúrában. A megoldások a megbízhatóságra, a méretezhetőségre, a kezelhetőségre és a nagyvállalati támogatásra összpontosítanak.

A WebLogic-kiszolgáló fürtök teljes mértékben engedélyezve vannak, hogy a WebLogic Kubernetes operátoron keresztül fussanak a Kubernetes-on (a továbbiakban az "operátor" Innentől kezdve). Az operátor a standard Kubernetes operátori mintát követi. Leegyszerűsíti a WebLogic-tartományok és-telepítések felügyeletét és működését a Kubernetes-ben, az egyéb manuális feladatok automatizálásával és további működési megbízhatósági funkciók hozzáadásával. A kezelő támogatja az Oracle WebLogic Server 12c, az Oracle Fusion middleware infrastruktúra 12c és azon kívül. A WebLogic Server 12.2.1.3 és a 12.2.1.4 hivatalos Docker-rendszerképeit teszteltük a kezelővel. Az operátorral kapcsolatos részletekért tekintse meg az [Oracle hivatalos dokumentációját](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Útmutatás, parancsfájlok és minták az WLS-on AK-on
A WebLogic-kiszolgáló az AK-on, az Oracle és a Microsoft közösen is biztosít részletes útmutatást, parancsfájlokat és mintákat a WebLogic-kiszolgáló AK-on való futtatásához. Az útmutató az [operátor dokumentációjának](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)Azure Kubernetes Service Sample szakaszába került. Az útmutató célja, hogy a lehető legegyszerűbbé váljon a termelési WebLogic-kiszolgáló használata az AK-beli központi telepítésekben. Az útmutató az Oracle által biztosított hivatalos WebLogic Server Docker-rendszerképeket használja. A feladatátvétel a Kubernetes állandó mennyiségi jogcímek által elért Azure Files keresztül érhető el. Az Azure Load Balancer a "terheléselosztó" típusú Kubernetes szolgáltatással való kiépítés esetén támogatott. Az útmutató nagy fokú konfigurációt és testreszabást tesz lehetővé.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="A WebLogic-kiszolgáló az AK-on való üzembe helyezéséhez használhatja a minta-parancsfájlokat.":::

Az útmutató jelenleg azt feltételezi, hogy a tartományt a Docker-rendszerképen kívül helyezi üzembe, és az Oracle standard Docker-rendszerképeit használja. Útmutatást adunk ahhoz, hogy az egyéni rendszerképeket a Docker-rendszerképen belül engedélyezzük a tartományon. A jövőben további egyszerű használat és Azure-szolgáltatások integrációja is lehetséges a piactéren, az Oracle WebLogic Server Azure Virtual Machines-megoldásokkal való tükrözése révén.

_Ezek a megoldások saját licenccel rendelkeznek_. Feltételezik, hogy már megkapta a megfelelő licenceket az Oracle-vel, és megfelelő licenccel rendelkezik az ajánlatok Azure-beli futtatásához.

_Ha szeretné, hogy az áttelepítési forgatókönyvek szorosan összhangban legyenek a megoldások fejlesztéséhez használt mérnöki csapattal, töltse ki [ezt a rövid kérdőívet](https://aka.ms/wls-on-azure-survey) , és adja meg a kapcsolattartási adatait_. A program-menedzserek, az építészek és a mérnökök hamarosan megközelítik Önt, és megkezdik a szoros együttműködést. Az áttelepítési forgatókönyvben való együttműködés lehetősége ingyenes, amíg a megoldások aktív kezdeti fejlesztés alatt állnak.

## <a name="deployment-architectures"></a>Üzembe helyezési architektúrák

Az Oracle WebLogic Server Azure Kubernetes szolgáltatásban való futtatására szolgáló megoldások széles körben lehetővé teszik az éles környezetben felépíthető üzembe helyezési architektúrák viszonylag könnyen elérhetővé tételét.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Az összetett WebLogic-kiszolgálók üzembe helyezése engedélyezve van az AK-ban":::

A megoldások az ügyfelek számára teljes rugalmasságot biztosítanak az üzemelő példányok testreszabásához. Valószínű, hogy az alkalmazások üzembe helyezése során az ügyfelek a további Azure-erőforrásokat is integrálják az üzemelő példányokkal. Javasoljuk, hogy visszajelzéseket nyújtson a felmérésben a megoldások továbbfejlesztése érdekében.

## <a name="next-steps"></a>További lépések

Ismerje meg az Oracle WebLogic Server futtatását az Azure Kubernetes szolgáltatásban.

> [!div class="nextstepaction"]
> [Útmutató, parancsfájlok és minták a WLS az AK-on való futtatásához](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes operátor](https://oracle.github.io/weblogic-kubernetes-operator/)