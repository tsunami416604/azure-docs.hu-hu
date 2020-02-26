---
title: Fogalmak – hozzáférés és identitás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) hozzáférését és identitását, beleértve a Azure Active Directory integrációt, az Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596368"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) hozzáférési és identitási lehetőségei

A hitelesítés és a biztonságos Kubernetes-fürtök többféleképpen is elvégezhető. A szerepköralapú hozzáférés-vezérlés (RBAC) használatával a felhasználók vagy csoportok csak a számukra szükséges erőforrások elérését biztosíthatják. Az Azure Kubernetes szolgáltatással (ak) Azure Active Directory használatával tovább javíthatja a biztonsági és az engedélyezési struktúrát is. Ezek a módszerek segítenek az alkalmazás számítási feladatainak és az ügyféladatok védelmében.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítséget nyújtanak az AK-beli engedélyek hitelesítéséhez és hozzárendeléséhez:

- [Kubernetes-szolgáltatásfiókok](#kubernetes-service-accounts)
- [Azure Active Directory integráció](#azure-active-directory-integration)
- [Szerepköralapú hozzáférés-vezérlés (RBAC)](#role-based-access-controls-rbac)
- [Szerepkörök és ClusterRoles](#roles-and-clusterroles)
- [RoleBindings és ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

A Kubernetes egyik elsődleges felhasználói típusa egy *szolgáltatásfiók*. Egy szolgáltatásfiók létezik a-ben, amelyet a a Kubernetes API felügyel. A szolgáltatásfiókok hitelesítő adatait a rendszer Kubernetes titokként tárolja, ami lehetővé teszi számukra, hogy az engedélyezett hüvelyek használják az API-kiszolgálóval való kommunikációt. A legtöbb API-kérelem hitelesítési jogkivonatot biztosít egy szolgáltatásfiók vagy egy normál felhasználói fiók számára.

A normál felhasználói fiókok több hagyományos hozzáférést tesznek lehetővé az emberi rendszergazdák vagy a fejlesztők számára, nem csupán a szolgáltatásokhoz és folyamatokhoz. A Kubernetes önmagában nem biztosít Identitáskezelés-kezelési megoldást, ahol a rendszer normál felhasználói fiókokat és jelszavakat tárol. Ehelyett a külső identitási megoldások integrálhatók a Kubernetes-be. AK-fürtök esetében ez az integrált Identity megoldás Azure Active Directory.

További információ a Kubernetes identitási lehetőségeiről: Kubernetes- [hitelesítés][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az AK-fürtök biztonsága javítható Azure Active Directory (AD) integrálásával. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás, amely az alapszintű címtárszolgáltatások, az alkalmazások és az identitások védelmét ötvözi. Az Azure AD lehetővé teszi, hogy a helyszíni identitásokat AK-fürtökbe integrálva egyetlen forrást biztosítson a fiókok kezeléséhez és biztonságához.

![Azure Active Directory-integráció AK-fürtökkel](media/concepts-identity/aad-integration.png)

Az Azure AD-vel integrált AK-fürtök segítségével a felhasználók vagy csoportok hozzáférést biztosíthatnak a Kubernetes-erőforrásokhoz a névtérben vagy a fürtön belül. `kubectl` konfigurációs környezet beszerzéséhez a felhasználó az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot futtathatja. Amikor egy felhasználó a `kubectl`használatával kommunikál az AK-fürttel, a rendszer felszólítja, hogy jelentkezzen be az Azure AD-beli hitelesítő adataival. Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok felügyeletéhez és a jelszó hitelesítő adataihoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokat érheti el.

Az AK-fürtökön futó Azure AD-hitelesítés OpenID connectet használ, amely az OAuth 2,0 protokollra épülő identitási réteg. A OAuth 2,0 a védett erőforrások eléréséhez szükséges hozzáférési jogkivonatok beszerzésére és használatára vonatkozó mechanizmusokat határoz meg, az OpenID Connect pedig a OAuth 2,0 engedélyezési folyamat kiterjesztését valósítja meg a hitelesítéshez. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][openid-connect]. Az Azure AD által az OpenID Connect használatával beszerzett hitelesítési tokenek ellenőrzéséhez az AK-fürtök Kubernetes webhook token-hitelesítést használnak. További információt a [webhook token hitelesítési dokumentációjában][webhook-token-docs]talál.

## <a name="role-based-access-controls-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A felhasználók által elvégezhető műveletek részletes szűrésének biztosításához a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez a vezérlési mechanizmus lehetővé teszi a felhasználók vagy felhasználói csoportok hozzárendelését, például az erőforrások létrehozását és módosítását, illetve a naplók megtekintését az alkalmazás-munkaterhelések futtatásához. Ezek az engedélyek egyetlen névtérre is érvényesek, vagy a teljes AK-fürtön keresztül is megadhatók. A Kubernetes RBAC segítségével létrehozhat *szerepköröket* az engedélyek definiálásához, majd hozzárendelheti ezeket a szerepköröket a felhasználókhoz *szerepkör-kötésekkel*.

További információ: RBAC- [hitelesítés használata][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)
Az erőforrásokhoz való hozzáférés szabályozásának egyik további mechanizmusa az Azure szerepköralapú hozzáférés-vezérlés (RBAC). A Kubernetes RBAC úgy van kialakítva, hogy az AK-fürtön belüli erőforrásokkal működjön, az Azure RBAC pedig az Azure-előfizetésen belüli erőforrásokkal való együttműködésre lett tervezve. Az Azure RBAC létrehoz egy szerepkör- *definíciót* , amely az alkalmazandó engedélyeket ismerteti. Ezután egy felhasználó vagy csoport rendeli hozzá ezt a szerepkör-definíciót egy adott *hatókörhöz*, amely lehet egy egyedi erőforrás, egy erőforráscsoport vagy az előfizetés egésze.

További információ: [Mi az az Azure RBAC?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Szerepkörök és ClusterRoles

Mielőtt engedélyeket rendel a felhasználókhoz a Kubernetes RBAC, először az engedélyeket *szerepkörként*kell megadnia. A Kubernetes szerepkörei engedélyeket *biztosítanak* . Nem létezik a *megtagadási* engedély fogalma.

A szerepkörök a névtéren belüli engedélyek megadására szolgálnak. Ha engedélyeket kell megadnia a teljes fürtön, vagy az erőforrásokat egy adott névtéren kívül kell megadnia, akkor Ehelyett használhatja a *ClusterRoles*.

A ClusterRole ugyanúgy működik, hogy engedélyeket biztosítson az erőforrásokhoz, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazható.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Ha a szerepkörök úgy vannak meghatározva, hogy engedélyeket adjanak az erőforrásoknak, akkor ezeket a Kubernetes RBAC engedélyeket egy *RoleBinding*kell rendelnie. Ha az AK-fürt integrálva van Azure Active Directory, akkor a kötések azt jelentik, hogy az Azure AD-felhasználók hogyan kapnak engedélyeket a fürtön belüli műveletek végrehajtásához.

A szerepkör-kötések egy adott névtér szerepköreinek hozzárendelésére szolgálnak. Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítse egyetlen AK-beli fürtöt, és a felhasználók csak a hozzárendelt névtérben lévő alkalmazás-erőforrásokat tudják elérni. Ha a szerepköröket a teljes fürtön kell megkötnie, vagy egy adott névtéren kívüli fürt erőforrásaira van szüksége, használhatja a *ClusterRoleBindings*.

A ClusterRoleBinding ugyanúgy működik, mint a szerepkörök felhasználókhoz való kötése, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazhatók. Ez a megközelítés lehetővé teszi, hogy a rendszergazdák vagy a támogatási mérnökök hozzáférjenek az AK-fürt összes erőforrásához.

## <a name="next-steps"></a>Következő lépések

Az Azure AD és a Kubernetes RBAC megismeréséhez tekintse meg a következőt: [Azure Active Directory integrálása az AK-val][aks-aad].

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK virtuális hálózatok][aks-concepts-network]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

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
