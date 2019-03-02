---
title: Alapelvei – hozzáférési és azonosító az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a hozzáférési és azonosító az Azure Kubernetes Service (AKS), beleértve az Azure Active Directory-integráció, a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC), és a szerepkörök és a kötéseket.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 64150438560d48575b4b65db4cdaf61aa0ada240
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243825"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és azonosító beállításai az Azure Kubernetes Service (AKS)

A hitelesítéshez, és tegye biztonságossá a Kubernetes-fürtök különböző módja van. Szerepköralapú hozzáférés-vezérlést (RBAC) révén biztosíthat a felhasználók vagy csoportok hozzáférés csak a számukra szükséges erőforrásokhoz. Az Azure Kubernetes Service (AKS), tovább javíthatja a biztonság és az engedélyek struktúra az Azure Active Directoryval. Ezek a módszerek segíti elő az alkalmazások számítási feladatai és az ügyféladatok védelmét.

Ez a cikk bemutatja az alapvető fogalmakat, amelyek segítségével hitelesíteni, és az engedélyeket az aks-ben:

- [Kubernetes-szolgáltatásfiókok](#kubernetes-service-accounts)
- [Az Azure Active Directory-integráció](#azure-active-directory-integration)
- [Szerepköralapú hozzáférés-vezérlést (RBAC)](#role-based-access-controls-rbac)
- [Szerepkörök és ClusterRoles](#roles-and-clusterroles)
- [RoleBindings és ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

Az elsődleges felhasználó begépeli a Kubernetes egyik egy *szolgáltatásfiók*. A szolgáltatási fiók létezik, és felügyeli, a Kubernetes API-t. Szolgáltatásfiókok hitelesítő adatait, Kubernetes titkos adatait, amely lehetővé teszi, hogy jogosult podok által az API-kiszolgálóval való kommunikációhoz használandó tárolódnak. A legtöbb API-kérések egy szolgáltatásfiók vagy egy normál felhasználói fiók számára biztosítson egy hitelesítési tokent.

Normál felhasználói fiókok hagyományosabb hozzáférést emberi rendszergazdák vagy a fejlesztők, nem csak szolgáltatások és folyamatokat. Kubernetes önmagában nem biztosít egy normál felhasználói fiókok és jelszavak tárolására identitáskezelési megoldás. Ehelyett a külső identitáskezelési megoldások Kubernetes integrálhatók. Az AKS-fürtök esetén az integrált megoldást az Azure Active Directoryban.

A Kubernetes identitáskezelési lehetőségei további információkért lásd: [Kubernetes hitelesítési][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az AKS-fürtök biztonsági fokozni lehet az Azure Active Directory (AD) integrációjával. Vállalati identitáskezelési évtizedes épülő Azure ad-ben egy több-bérlős, felhőalapú címtár, és identitáskezelési szolgáltatása, amely ötvözi az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-felügyeletet és az identity protection. Az Azure ad-vel a helyszíni identitások integrálható az AKS-fürtök egyetlen forrásként nyújt a fiók felügyeleti és biztonsági.

![Az AKS-fürtök az Azure Active Directory-integráció](media/concepts-identity/aad-integration.png)

Az Azure Active Directoryba integrált AKS-fürtökkel biztosíthat a felhasználók vagy csoportok hozzáférést egy adott névtéren belül, vagy a fürtben Kubernetes-erőforrást. Beszerzése egy `kubectl` konfigurációs környezet, a felhasználó futtathatja a [az aks get-credentials] [ az-aks-get-credentials] parancsot. Amikor egy felhasználó majd kommunikál az AKS-fürtöt `kubectl`, bekapcsolják jelentkezzen be az Azure AD hitelesítő adatait. Ez a megközelítés egy egyetlen forrásból felhasználóifiók-kezelés és a jelszavas hitelesítő adatokat biztosít. A felhasználó csak akkor tudja elérni az erőforrásokat, a fürt rendszergazdája által meghatározott módon.

Az AKS-fürtök az Azure AD-hitelesítést használ, OpenID Connect, az identitási rétegben épülő az OAuth 2.0 protokollt. OAuth 2.0 mechanizmusok beszerzése és a védett erőforrások eléréséhez hozzáférési jogkivonatok használatával határozza meg, és OpenID Connect hitelesítést valósít meg az OAuth 2.0 engedélyezési folyamat bővítményeként. Az OpenID Connect további információkért lásd: a [Open ID Connect dokumentáció][openid-connect]. AKS-fürtök révén OpenID Connect Azure AD-ből kapott a hitelesítési tokenek ellenőrzéséhez használja a Kubernetes Webhook Eszközjogkivonattal történő hitelesítés. További információkért lásd: a [Webhook tokent használó hitelesítés biztosítását dokumentáció][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Szerepköralapú hozzáférés-vezérlést (RBAC)

Ahhoz, hogy a felhasználók által végrehajtható műveletek részletes szűrést, a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) használ. A vezérlési mechanizmus lehetővé teszi a felhasználók hozzárendelése, vagy felhasználói számára többek között a csoportok hozzon létre vagy módosítsa erőforrások, vagy az alkalmazás-alapú számítási feladatait a naplók megtekintéséhez. Ezek az engedélyek hatóköre egyetlen névtér, vagy a teljes AKS-fürtön kapnak. A Kubernetes RBAC, létrehozhat *szerepkörök* engedélyeket határozhat meg, és hozzárendelheti ezeket a szerepköröket a felhasználók *szerepkör kötések*.

További információkért lásd: [használatával RBAC-hitelesítés][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Az Azure szerepköralapú hozzáférés-vezérlők (RBAC)
Egy kiegészítő mechanizmust erőforrásokhoz való hozzáférés szabályozása Azure szerepköralapú hozzáférés-vezérlők (RBAC). Kubernetes RBAC úgy tervezték, hogy az AKS-fürt belüli erőforrásokon működjön, és az Azure RBAC úgy tervezték, hogy az Azure-előfizetésen belüli erőforrásokon működjön. Az Azure RBAC, hozzon létre egy *szerepkör-definíció* , amely ismerteti a alkalmazni az engedélyeket. Egy felhasználó vagy csoport a rendszer hozzárendeli a szerepkör-definíció egy adott *hatókör*, amely egyedi erőforrásokat, csoport vagy az előfizetés erőforrás lehet.

További információkért lásd: [Mi az Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Szerepkörök és ClusterRoles

Mielőtt Kubernetes RBAC-engedélyek rendel felhasználókhoz, először meghatározhatja ezeket az engedélyeket egy *szerepkör*. Kubernetes-szerepkörök *biztosítson* engedélyeket. Nem létezik a fogalma nem egy *megtagadása* engedéllyel.

Szerepkörök segítségével engedélyek egy adott névtéren belül. Engedélyek megadása az egész fürt között, vagy a fürt erőforrásai kívül egy adott névtér van szüksége, ha ehelyett használható *ClusterRoles*.

Egy ClusterRole engedélyeket az erőforrásokhoz ugyanúgy működik, de az erőforrások között a teljes fürtöt, egy adott névtér nem alkalmazható.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Miután a szerepkörök vannak megadva, hogy engedélyeket az erőforrásokhoz, hozzárendelhet-e ezeket a Kubernetes RBAC-engedélyek egy *RoleBinding*. Az AKS-fürt együttműködik az Azure Active Directoryval, ha a kötések, hogyan ezeket az Azure AD-felhasználók kapnak-e jogosultságokkal a fürtön belül műveletek végrehajtásához.

Szerepkör-kötések segítségével egy adott névtér szerepkörök hozzárendelése. Ez a megközelítés lehetővé teszi egy egy AKS-fürtöt, a felhasználók csak fér hozzá az alkalmazás erőforrásokhoz a hozzárendelt névtér logikailag elkülönítse. Ha kell kötni a szerepkörök, az egész fürt között, vagy a fürt erőforrásai kívül egy adott névtér, Ehelyett használhatja *ClusterRoleBindings*.

Egy ClusterRoleBinding kötést létrehozni a szerepkörök felhasználókhoz ugyanúgy működik, de az erőforrások között a teljes fürtöt, egy adott névtér nem alkalmazható. Ez a megközelítés lehetővé teszi biztosítása a rendszergazdák vagy az AKS-fürtöt a mérnökök erőforrásokhoz való teljes hozzáférés támogatásához.

## <a name="next-steps"></a>További lépések

Ismerkedés az Azure ad-vel és a Kubernetes RBAC, lásd: [integrálása az Azure Active Directory az aks-sel][aks-aad].

További kapcsolódó ajánlott eljárások: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][operator-best-practices-identity].

És további információkat az alapvető Kubernetes AKS fogalmait tekintse meg a következő cikkeket:

- [Kubernetes AKS-fürtök / és a számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes / AKS biztonsági][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes és az AKS-tároló][aks-concepts-storage]
- [Kubernetes AKS méretezése /][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
