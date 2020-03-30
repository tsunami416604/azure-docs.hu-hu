---
title: Fogalmak – Hozzáférés és identitás az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a hozzáférést és az identitást az Azure Kubernetes-szolgáltatásban (AKS), beleértve az Azure Active Directory-integrációt, a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259603"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz

A Kubernetes-fürtökkel való hitelesítés különböző módokon hitelesíthető. Szerepköralapú hozzáférés-vezérlés (RBAC) használatával csak a szükséges erőforrásokhoz adhat hozzáférést a felhasználóknak vagy csoportoknak. Az Azure Kubernetes-szolgáltatás (AKS) segítségével tovább növelheti a biztonsági és engedélyszerkezetet az Azure Active Directory használatával. Ezek a megközelítések segítenek az alkalmazás-munkaterhelések és az ügyféladatok védelmében.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítenek az AKS-ben az engedélyek hitelesítésében és hozzárendelésében:

- [Kubernetes szolgáltatásfiókok](#kubernetes-service-accounts)
- [Azure Active Directory-integráció](#azure-active-directory-integration)
- [Szerepköralapú hozzáférés-vezérlés (RBAC)](#role-based-access-controls-rbac)
- [Szerepkörök és fürtszerepkörök](#roles-and-clusterroles)
- [Szerepkörkötések és ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes szolgáltatásfiókok

A Kubernetes egyik elsődleges felhasználói típusa egy *szolgáltatásfiók.* A Kubernetes API-ban létezik és a Kubernetes API-ban létezik egy szolgáltatásfiók. A szolgáltatásfiókok hitelesítő adatait Kubernetes titkos kulcsok ként tárolja, amely lehetővé teszi, hogy az engedélyezett podok használják az API-kiszolgálóval való kommunikációhoz. A legtöbb API-kérelem hitelesítési jogkivonatot biztosít egy szolgáltatásfiókhoz vagy egy normál felhasználói fiókhoz.

A normál felhasználói fiókok hagyományosabb hozzáférést biztosítanak az emberi rendszergazdáknak vagy fejlesztőknek, nem csak a szolgáltatásoknak és a folyamatoknak. A Kubernetes nem biztosít identitáskezelési megoldást, ahol rendszeres felhasználói fiókok és jelszavak tárolódnak. Ehelyett a külső identitásmegoldások integrálhatók a Kubernetes-be. Az AKS-fürtök esetében ez az integrált identitáskezelési megoldás az Azure Active Directory.

A Kubernetes identitáskezelési beállításairól a [Kubernetes-hitelesítés][kubernetes-authentication]című témakörben talál további információt.

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az AKS-fürtök biztonsága az Azure Active Directory (AD) integrálásával növelhető. A több évtizedes vállalati identitáskezelésre épülő Azure AD egy több-bérlős, felhőalapú címtár- és identitáskezelési szolgáltatás, amely egyesíti az alapvető címtárszolgáltatásokat, az alkalmazáshozzáférés-kezelést és az identitásvédelmet. Az Azure AD-vel integrálhatja a helyszíni identitásokat az AKS-fürtökbe, hogy egyetlen forrást biztosítson a fiókkezeléshez és -biztonsághoz.

![Az Azure Active Directory integrációja Az AKS-fürtökkel](media/concepts-identity/aad-integration.png)

Az Azure AD-be integrált AKS-fürtökkel felhasználóknak vagy csoportoknak hozzáférést biztosíthat a Kubernetes-erőforrásokhoz egy névtérben vagy a fürtön belül. `kubectl` Konfigurációs környezet beszerzéséhez a felhasználó futtathatja az [aks get-credentials parancsot.][az-aks-get-credentials] Amikor egy felhasználó ezután interakcióba lép `kubectl`az AKS-fürttel, a rendszer kéri, hogy jelentkezzen be az Azure AD-hitelesítő adataival. Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok kezeléséhez és a jelszóhitelesítő adatokhoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokhoz férhet hozzá.

Az AKS-fürtökben az Azure AD-hitelesítés az OpenID Connect protokollt használja, amely az OAuth 2.0 protokollra épülő identitásréteg. Az OAuth 2.0 a védett erőforrások eléréséhez hozzáférési jogkivonatok beszerzésére és használatára szolgáló mechanizmusokat határoz meg, az OpenID Connect pedig az OAuth 2.0 engedélyezési folyamatának kiterjesztéseként valósítja meg a hitelesítést. Az OpenID Connectről az [Open ID Connect dokumentációjában olvashat bővebben.][openid-connect] Az Azure AD-ből az OpenID Connecten keresztül beszerzett hitelesítési jogkivonatok ellenőrzéséhez az AKS-fürtök kubernetes webhook token hitelesítést használnak. További információt a [Webhook token hitelesítési dokumentációjában][webhook-token-docs]talál.

## <a name="role-based-access-controls-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A felhasználók által végrehajtható műveletek részletes szűrésének biztosításához a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez a vezérlési mechanizmus lehetővé teszi, hogy felhasználókat vagy felhasználói csoportokat rendeljen hozzá például erőforrások létrehozására vagy módosítására, illetve naplóinak megtekintésére a futó alkalmazás-munkaterhelésekből. Ezek az engedélyek egyetlen névtérre is hatókörözhetők, vagy a teljes AKS-fürtben adhatók meg. A Kubernetes RBAC segítségével *szerepköröket* hozhat létre az engedélyek definiálásához, majd ezeket a szerepkörökhöz rendelheti a *szerepkörkötésekkel rendelkező felhasználókhoz.*

További információ: [Az RBAC-engedélyezés használata.][kubernetes-rbac]

### <a name="azure-role-based-access-controls-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)
Az erőforrásokhoz való hozzáférés szabályozásának egyik további mechanizmusa az Azure szerepköralapú hozzáférés-vezérlés (RBAC). A Kubernetes RBAC-ot úgy tervezték, hogy az AKS-fürtön belüli erőforrásokon dolgozzon, az Azure RBAC pedig úgy lett kialakítva, hogy az Azure-előfizetésen belüli erőforrásokon dolgozzon. Az Azure RBAC segítségével hozzon létre egy *szerepkör-definíciót,* amely felvázolja az alkalmazandó engedélyeket. Ezután egy felhasználó vagy csoport hozzávan rendelve ehhez a szerepkör-definícióhoz egy adott *hatókörhöz*, amely lehet egy adott erőforrás, egy erőforráscsoport vagy az előfizetés egészében.

További információ: [Mi az Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Szerepkörök és fürtszerepkörök

Mielőtt engedélyeket rendelne a Kubernetes RBAC-mal rendelkező felhasználókhoz, először ezeket az engedélyeket *szerepkörként*határozza meg. A Kubernetes szerepkörök engedélyeket *adnak.* Nincs fogalma megtagadása *engedélyt.*

A szerepkörök a névtéren belüli engedélyek megadására szolgálnak. Ha engedélyeket kell megadnia a teljes fürtön, vagy egy adott névtéren kívüli erőforrásokat kell megadnia, használhatja a *ClusterRoles programot.*

A ClusterRole ugyanúgy működik, hogy engedélyeket adjon az erőforrásoknak, de a teljes fürt erőforrásaira alkalmazható, nem egy adott névtérre.

## <a name="rolebindings-and-clusterrolebindings"></a>Szerepkörkötések és ClusterRoleBindings

Miután a szerepkörök definiálva vannak az erőforrások engedélyek megadásához, ezeket a Kubernetes RBAC-engedélyeket *egy RoleBinding-hez*rendeli hozzá. Ha az AKS-fürt integrálható az Azure Active Directoryval, a kötések azok az Azure AD-felhasználók számára engedélyezett ek a fürtön belüli műveletek végrehajtására.

A szerepkörkötések egy adott névtér szerepkörök hozzárendelésére szolgálnak. Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítsen egy AKS-fürtöt, és a felhasználók csak a hozzárendelt névtérben férhetnek hozzá az alkalmazás erőforrásaihoz. Ha a szerepköröket a teljes fürthöz kell kötnie, vagy egy adott névtéren kívüli erőforrásokat kell *létrehoznia, használhatja a ClusterRoleBindings parancsot.*

A ClusterRoleBinding ugyanúgy működik, hogy szerepköröket kössön a felhasználókhoz, de a teljes fürt erőforrásaira alkalmazható, nem pedig egy adott névtérre. Ez a megközelítés lehetővé teszi, hogy a rendszergazdák vagy a támogatási mérnökök hozzáférést az AKS-fürt összes erőforrásához.

## <a name="next-steps"></a>További lépések

Az Azure AD és a Kubernetes RBAC ismerkedése: [Az Azure Active Directory integrálása az AKS-sel.][aks-aad]

A kapcsolódó gyakorlati tanácsokról az [AKS-hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok című témakörben található.][operator-best-practices-identity]

A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS-fürtök és munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes / AKS biztonság][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes / AKS tároló][aks-concepts-storage]
- [Kubernetes / AKS skála][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
