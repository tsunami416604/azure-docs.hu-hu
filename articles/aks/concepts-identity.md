---
title: Fogalmak – hozzáférés és identitás az Azure Kubernetes Servicesben (ak)
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) hozzáférését és identitását, beleértve a Azure Active Directory integrációt, az Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC), valamint a szerepköröket és kötéseket.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: b7739320fa23bf4469548f61486da1a5ee6110da
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507155"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Hozzáférési és identitás-beállítások az Azure Kubernetes Service (AKS) szolgáltatáshoz

A hitelesítés, a hozzáférés/engedélyezés és a biztonságos Kubernetes-fürtök többféleképpen is megadhatók. A Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) használatával a felhasználók, csoportok és szolgáltatásfiókok hozzáférése csak a szükséges erőforrásokhoz biztosítható. Az Azure Kubernetes Service (ak) segítségével tovább növelheti a biztonsági és az engedélyezési struktúrát Azure Active Directory és az Azure RBAC használatával. Ezek a módszerek segítenek a fürt hozzáférésének biztonságossá tételében, és csak a minimálisan szükséges engedélyeket biztosítják a fejlesztők és a kezelők számára.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítséget nyújtanak az AK-beli engedélyek hitelesítéséhez és hozzárendeléséhez:

- [Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)](#kubernetes-role-based-access-controls-rbac)
  - [Szerepkörök és ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings és ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Kubernetes-szolgáltatásfiókok](#kubernetes-service-accounts)
- [Azure Active Directory-integráció](#azure-active-directory-integration)
- [Azure-RBAC](#azure-role-based-access-controls-rbac)
  - [Azure-RBAC az AK-erőforráshoz való hozzáférés engedélyezéséhez](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Azure RBAC for Kubernetes-engedélyezés (előzetes verzió)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-controls-rbac"></a>Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)

A felhasználók által elvégezhető műveletek részletes szűrésének biztosításához a Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) használ. Ez a vezérlési mechanizmus lehetővé teszi a felhasználók vagy felhasználói csoportok hozzárendelését, például az erőforrások létrehozását és módosítását, illetve a naplók megtekintését az alkalmazás-munkaterhelések futtatásához. Ezek az engedélyek egyetlen névtérre is érvényesek, vagy a teljes AK-fürtön keresztül is megadhatók. A Kubernetes RBAC segítségével létrehozhat *szerepköröket* az engedélyek definiálásához, majd hozzárendelheti ezeket a szerepköröket a felhasználókhoz *szerepkör-kötésekkel*.

További információ: RBAC- [hitelesítés használata][kubernetes-rbac].


### <a name="roles-and-clusterroles"></a>Szerepkörök és ClusterRoles

Mielőtt engedélyeket rendel a felhasználókhoz a Kubernetes RBAC, először az engedélyeket *szerepkörként*kell megadnia. A Kubernetes szerepkörei engedélyeket *biztosítanak* . Nincs a *megtagadási* engedély fogalma.

A szerepkörök a névtéren belüli engedélyek megadására szolgálnak. Ha engedélyeket kell megadnia a teljes fürtön, vagy az erőforrásokat egy adott névtéren kívül kell megadnia, akkor Ehelyett használhatja a *ClusterRoles*.

A ClusterRole ugyanúgy működik, hogy engedélyeket biztosítson az erőforrásokhoz, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazható.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings és ClusterRoleBindings

Ha a szerepkörök úgy vannak meghatározva, hogy engedélyeket adjanak az erőforrásoknak, akkor ezeket a Kubernetes RBAC engedélyeket egy *RoleBinding*kell rendelnie. Ha az AK-fürt [integrálva van Azure Active Directoryokkal](#azure-active-directory-integration), a kötések azt ismertetik, hogy az Azure ad-felhasználók hogyan kapnak engedélyeket a fürtön belüli műveletek végrehajtásához: Hogyan [vezérelheti a hozzáférést a fürterőforrások a szerepköralapú hozzáférés-vezérlés és a Azure Active Directory identitások használatával](azure-ad-rbac.md).

A szerepkör-kötések egy adott névtér szerepköreinek hozzárendelésére szolgálnak. Ez a megközelítés lehetővé teszi, hogy logikailag elkülönítse egyetlen AK-beli fürtöt, és a felhasználók csak a hozzárendelt névtérben lévő alkalmazás-erőforrásokat tudják elérni. Ha a szerepköröket a teljes fürtön kell megkötnie, vagy egy adott névtéren kívüli fürt erőforrásaira van szüksége, használhatja a *ClusterRoleBindings*.

A ClusterRoleBinding ugyanúgy működik, mint a szerepkörök felhasználókhoz való kötése, de a teljes fürtön lévő erőforrásokra, nem pedig egy adott névtérre is alkalmazhatók. Ez a megközelítés lehetővé teszi, hogy a rendszergazdák vagy a támogatási mérnökök hozzáférjenek az AK-fürt összes erőforrásához.


> [!NOTE]
> A Microsoft/AK által végrehajtott összes művelet felhasználói beleegyezett a beépített Kubernetes `aks-service` -szerepkör és a beépített szerepkör-kötés alá `aks-service-rolebinding` . Ez a szerepkör lehetővé teszi az AK számára a fürtökkel kapcsolatos problémák hibaelhárítását és diagnosztizálását, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket, illetve egyéb magas jogosultsági A szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel. Tudjon meg többet az [AK-támogatási szabályzatokról](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Kubernetes-szolgáltatásfiókok

A Kubernetes egyik elsődleges felhasználói típusa egy *szolgáltatásfiók*. Egy szolgáltatásfiók létezik a-ben, amelyet a a Kubernetes API felügyel. A szolgáltatásfiókok hitelesítő adatait a rendszer Kubernetes titokként tárolja, ami lehetővé teszi számukra, hogy az engedélyezett hüvelyek használják az API-kiszolgálóval való kommunikációt. A legtöbb API-kérelem hitelesítési jogkivonatot biztosít egy szolgáltatásfiók vagy egy normál felhasználói fiók számára.

A normál felhasználói fiókok több hagyományos hozzáférést biztosítanak az emberi rendszergazdáknak és a fejlesztőknek, nem csupán szolgáltatásoknak és folyamatoknak. A Kubernetes önmagában nem biztosít Identitáskezelés-kezelési megoldást, ahol a rendszer normál felhasználói fiókokat és jelszavakat tárol. Ehelyett a külső identitási megoldások integrálhatók a Kubernetes-be. AK-fürtök esetében ez az integrált Identity megoldás Azure Active Directory.

További információ a Kubernetes identitási lehetőségeiről: Kubernetes- [hitelesítés][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integráció

Az AK-fürtök biztonsága javítható Azure Active Directory (AD) integrálásával. Az Azure AD egy több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatás, amely az alapszintű címtárszolgáltatások, az alkalmazások és az identitások védelmét ötvözi. Az Azure AD lehetővé teszi, hogy a helyszíni identitásokat AK-fürtökbe integrálva egyetlen forrást biztosítson a fiókok kezeléséhez és biztonságához.

![Azure Active Directory-integráció AK-fürtökkel](media/concepts-identity/aad-integration.png)

Az Azure AD-vel integrált AK-fürtök segítségével a felhasználók vagy csoportok hozzáférést biztosíthatnak a Kubernetes-erőforrásokhoz a névtérben vagy a fürtön belül. A konfigurációs környezet beszerzéséhez a `kubectl` felhasználó az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot futtathatja. Ha a felhasználó ezt követően együttműködik az AK-fürttel `kubectl` , a rendszer felszólítja, hogy jelentkezzen be az Azure ad-beli hitelesítő adataival. Ez a megközelítés egyetlen forrást biztosít a felhasználói fiókok felügyeletéhez és a jelszó hitelesítő adataihoz. A felhasználó csak a fürt rendszergazdája által meghatározott erőforrásokat érheti el.

Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][openid-connect]. A Kubernetes-fürtön belül a rendszer [webhook jogkivonat-hitelesítést][webhook-token-docs] használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

A Kubernetes-fürtön belül a rendszer webhook jogkivonat-hitelesítést használ a hitelesítési tokenek ellenőrzéséhez. A webhook-jogkivonat hitelesítése az AK-fürt részeként van konfigurálva és felügyelve.

### <a name="webhook-and-api-server"></a>Webhook és API-kiszolgáló

![Webhook és API-kiszolgáló hitelesítési folyamata](media/concepts-identity/auth-flow.png)

Ahogy az a fenti ábrán is látható, az API-kiszolgáló meghívja az AK webhook-kiszolgálót, és a következő lépéseket hajtja végre:

1. Az Azure AD ügyfélalkalmazás a kubectl használatával jelentkezik be a OAuth 2,0-es [eszköz-engedélyezési folyamattal](../active-directory/develop/v2-oauth2-device-code.md)rendelkező felhasználókba.
2. Az Azure AD egy access_token, id_token és egy refresh_token biztosít.
3. A felhasználó kérést küld a kubectl access_token a kubeconfig.
4. A Kubectl elküldi a access_tokent a APIServer.
5. Az API-kiszolgáló a hitelesítési webhook-kiszolgálóval van konfigurálva az érvényesítés végrehajtásához.
6. A hitelesítési webhook-kiszolgáló megerősíti, hogy a JSON Web Token aláírása érvényes az Azure AD nyilvános aláíró kulcsának ellenőrzésével.
7. A kiszolgálóalkalmazás felhasználó által megadott hitelesítő adatok használatával kérdezi le a bejelentkezett felhasználó csoporttagságait az MS Graph APIból.
8. A rendszer választ kap a APIServer olyan felhasználói adatokkal, mint például a hozzáférési jogkivonat egyszerű felhasználóneve (UPN) jogcíme, valamint a felhasználó csoporttagság az objektumazonosító alapján.
9. Az API a Kubernetes szerepkör/RoleBinding alapján hajt végre engedélyezési döntést.
10. Az engedélyezést követően az API-kiszolgáló egy választ küld a kubectl.
11. A Kubectl visszajelzést nyújt a felhasználónak.
 
**Ismerje meg, hogyan integrálhatja az AK-t [a HRE-](managed-aad.md)mel.**

## <a name="azure-role-based-access-controls-rbac"></a>Azure szerepköralapú hozzáférés-vezérlés (RBAC)

Az Azure RBAC egy [Azure Resource Managerra](../azure-resource-manager/management/overview.md) épülő engedélyezési rendszer, amely részletes hozzáférés-vezérlést biztosít az Azure-erőforrásokhoz.

 Az Azure RBAC úgy van kialakítva, hogy az Azure-előfizetésen belüli erőforrásokkal működjön, miközben a Kubernetes RBAC úgy van kialakítva, hogy az AK-fürtön belüli Kubernetes 

Az Azure RBAC létrehoz egy szerepkör- *definíciót* , amely az alkalmazandó engedélyeket ismerteti. A felhasználó vagy csoport ezt követően egy adott *hatókörhöz*tartozó *szerepkör-hozzárendeléssel* rendeli hozzá ezt a szerepkör-definíciót, amely lehet önálló erőforrás, erőforráscsoport vagy az előfizetés egésze.

További információ: [Mi az az Azure RBAC?][azure-rbac]

Az AK-fürtök teljes körű működtetéséhez két hozzáférési szint szükséges: 
1. [Hozzáférés az AK-erőforráshoz az Azure-előfizetésében](#azure-rbac-to-authorize-access-to-the-aks-resource). Ezzel a folyamattal vezérelheti a fürtök méretezését vagy frissítését az AK API-k használatával, valamint a kubeconfig lekérésével.
2. Hozzáférés a Kubernetes API-hoz. Ezt a hozzáférést a [KUBERNETES RBAC](#kubernetes-role-based-access-controls-rbac) (hagyományos) vagy az [Azure RBAC és a Kubernetes-hitelesítés integrálásával](#azure-rbac-for-kubernetes-authorization-preview) vezérelheti

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Azure-RBAC az AK-erőforráshoz való hozzáférés engedélyezéséhez

Az Azure RBAC segítségével megadhatja a felhasználók (vagy identitások) számára az AK-erőforrások részletes elérését egy vagy több előfizetésben. Előfordulhat például, hogy az [Azure Kubernetes szolgáltatás közreműködői szerepköre](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) lehetővé teszi, hogy olyan műveleteket hajtson végre, mint a fürt skálázása és frissítése. Míg egy másik felhasználó rendelkezhet az [Azure Kubernetes Service cluster rendszergazdai szerepkörrel](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) , amely csak engedélyt ad a rendszergazdai kubeconfig lekérésére.

Másik lehetőségként megadhatja a felhasználónak az általános [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkört is, amely magában foglalja a fenti engedélyeket és minden lehetséges műveletet az AK-erőforráson, kivéve az engedélyek felügyeletét.

További információ az Azure RBAC használatával a kubeconfig-fájlhoz való hozzáférés biztonságossá tételéhez, amely hozzáférést biztosít a Kubernetes [API-](control-kubeconfig-access.md)hoz.

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC for Kubernetes-engedélyezés (előzetes verzió)

Az Azure RBAC-integrációjában az AK egy Kubernetes-hitelesítési webhook-kiszolgálót használ, amely lehetővé teszi az Azure AD-ben integrált K8s-fürterőforrások engedélyeinek és hozzárendeléseinek kezelését az Azure RBAC szerepkör-definíciója és a szerepkör-hozzárendelések használatával.

![Azure-RBAC az Kubernetes engedélyezési folyamatához](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Ahogy a fenti ábrán is látható, az Azure RBAC-integráció használatakor a Kubernetes API-nak küldött összes kérelem ugyanezt a hitelesítési folyamatot fogja követni, ahogy az az [Azure Active Integration szakaszban](#azure-active-directory-integration)is látható. 

De ezt követően, ahelyett, hogy kizárólag az Kubernetes RBAC-ra támaszkodik, a kérést az Azure engedélyezi, feltéve, hogy a kérést használó identitás létezik a HRE-ben. Ha az identitás nem létezik a HRE, például egy Kubernetes-szolgáltatásfiók, akkor az Azure-RBAC nem indul el, és ez lesz a normál Kubernetes RBAC.

Ebben a forgatókönyvben az egyik négy beépített szerepkört is megadhatja a felhasználóknak, vagy létrehozhat egyéni szerepköröket, mint a Kubernetes szerepköreivel, de ebben az esetben az Azure RBAC-mechanizmusok és API-k használatával. 

Ez a funkció lehetővé teszi, hogy például ne csak a felhasználók számára engedélyezze az AK-erőforrást az előfizetések között, de be kell állítania és hozzá kell adni azokat a szerepköröket és engedélyeket, amelyek a Kubernetes API-hoz való hozzáférést vezérlik a fürtben. Például megadhatja a `Azure Kubernetes Service RBAC Viewer` szerepkört az előfizetés hatókörében, és a címzettje minden fürtből listázhatja és lekérheti az összes Kubernetes objektumot, de nem módosíthatja azokat.


#### <a name="built-in-roles"></a>Beépített szerepkörök

Az AK a következő négy beépített szerepkört biztosítja. Hasonlóak a [Kubernetes beépített szerepköreihez](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , de néhány különbséggel, például a CRDs támogatásával. Az egyes beépített szerepkörök által engedélyezett műveletek teljes listáját [itt](../role-based-access-control/built-in-roles.md)találja.

| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Az Azure Kubernetes Service RBAC megjelenítője  | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Nem teszi lehetővé a szerepkörök és a szerepkör-kötések megtekintését. Ez a szerepkör nem teszi lehetővé a megtekintést `Secrets` , mivel a titkok tartalmának olvasása lehetővé teszi `ServiceAccount` a névtérben lévő hitelesítő adatok elérését, ami lehetővé TENNÉ az API `ServiceAccount` -k elérését a névtérben (a jogosultságok eszkalációjának formája).  |
| Az Azure Kubernetes Service RBAC-írója | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a `Secrets` hüvelyek elérését és futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely SERVICEACCOUNT API-hozzáférési szintjeinek megszerzésére. |
| Az Azure Kubernetes Service RBAC rendszergazdája  | A rendszergazdai hozzáférés engedélyezése a névtéren belül. Írási/olvasási hozzáférés engedélyezése a névtér (vagy a fürt hatóköre) legtöbb erőforrásához, beleértve a szerepkörök és a szerepkör-kötések létrehozását a névtéren belül. Ez a szerepkör nem engedélyez írási hozzáférést az erőforrás-kvótához vagy magához a névtérhez. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi a felügyelők számára, hogy bármilyen műveletet végezzenek bármilyen erőforráson. Teljes hozzáférést biztosít a fürt összes erőforrásához és az összes névtérhez. |

**Tekintse meg, hogyan használhat Azure RBAC for Kubernetes-hitelesítést [itt](manage-azure-rbac.md).**

## <a name="next-steps"></a>Következő lépések

- Az Azure AD és a Kubernetes RBAC megismeréséhez tekintse meg a következőt: [Azure Active Directory integrálása az AK-val][aks-aad].
- A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a hitelesítéshez és engedélyezéshez az AK-ban][operator-best-practices-identity].
- Az Azure RBAC Kubernetes-hitelesítéssel való használatának megkezdéséhez lásd: az Azure [RBAC használata az Azure Kubernetes Service (ak) fürtön belüli hozzáférés engedélyezéséhez](manage-azure-rbac.md).
- A kubeconfig-fájl védelmének megkezdéséhez lásd: [a fürt konfigurációs fájljához való hozzáférés korlátozása](control-kubeconfig-access.md)

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
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
