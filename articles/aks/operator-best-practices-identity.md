---
title: Operátori gyakorlati tanácsok – Identitás az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a fürtfelelősök ajánlott eljárásokat, hogyan kezelheti a hitelesítést és az engedélyezést a fürtök az Azure Kubernetes szolgáltatás (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 5ff5bdaced46a20dec3e7c5d7fb029f9428a12f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594770"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) hitelesítésével és engedélyezésével kapcsolatos gyakorlati tanácsok

A fürtök üzembe helyezése és karbantartása az Azure Kubernetes-szolgáltatás (AKS) telepítésekor és karbantartásakor az erőforrásokhoz és szolgáltatásokhoz való hozzáférés kezelésének módjait kell megvalósítania. Ezek nélkül a vezérlők nélkül a fiókok hozzáférhetnek azokhoz az erőforrásokhoz és szolgáltatásokhoz, amelyekre nincs szükségük. Azt is nehéz nyomon követni, hogy mely hitelesítő adatokat használták a módosításokhoz.

Ez az ajánlott eljárások ról szóló cikk arra összpontosít, hogy a fürtkezelő hogyan kezelheti az AKS-fürtök hozzáférését és identitását. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * AKS-fürtfelhasználók hitelesítése az Azure Active Directoryval
> * Erőforrásokhoz való hozzáférés szabályozása szerepköralapú hozzáférés-vezérléssel (RBAC)
> * Felügyelt identitás használata más szolgáltatásokkal való hitelesítéshez

## <a name="use-azure-active-directory"></a>Az Azure Active Directory használata

**Gyakorlati tanácsok –** AKS-fürtök üzembe helyezése az Azure AD-integrációval. Az Azure AD használatával központosítja az identitáskezelési összetevőt. A felhasználói fiók vagy csoport állapotában bekövetkező változások automatikusan frissülnek az AKS-fürthöz való hozzáférésben. A következő szakaszban tárgyalt szerepkörök vagy fürtszerepkörök és kötések használatával a felhasználókat vagy csoportokat a szükséges engedélyek legkisebb összegére scopepel vetheti.

A Kubernetes-fürt fejlesztőinek és alkalmazástulajdonosainak különböző erőforrásokhoz kell hozzáférnie. A Kubernetes nem biztosít identitáskezelési megoldást annak szabályozására, hogy mely felhasználók milyen erőforrásokkal kommunikálhatnak. Ehelyett általában integrálja a fürtegy meglévő identitáskezelési megoldás. Az Azure Active Directory (AD) vállalati használatra kész identitáskezelési megoldást biztosít, és integrálható az AKS-fürtökkel.

Az AKS-ben az Azure AD-be integrált fürtökkel olyan *szerepköröket* vagy *fürtszerepköröket* hozhat létre, amelyek hozzáférési engedélyeket határoznak meg az erőforrásokhoz. Ezután az Azure AD-ből a szerepköröket felhasználókhoz vagy csoportokhoz *köti.* Ezeket a Kubernetes szerepköralapú hozzáférés-vezérléseket (RBAC) a következő szakasz ismerteti. Az Azure AD integrációja és az erőforrásokhoz való hozzáférés szabályozásának szabályozása az alábbi ábrán látható:

![Fürtszintű hitelesítés az Azure Active Directory és az AKS integrációjához](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. A fejlesztő az Azure AD-vel hitelesíti magát.
1. Az Azure AD token kiállítási végpont jattaszi a hozzáférési jogkivonatot.
1. A fejlesztő egy műveletet hajt végre az Azure AD-token használatával, például`kubectl create pod`
1. A Kubernetes érvényesíti a jogkivonatot az Azure Active Directoryval, és lekéri a fejlesztő csoporttagságait.
1. Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) és a fürt házirendek alkalmazva.
1. A fejlesztő ikérése sikeres vagy nem az Azure AD csoporttagság és a Kubernetes RBAC és szabályzatok korábbi érvényesítésén alapul.

Az Azure AD-t használó AKS-fürt létrehozásáról az [Azure Active Directory integrálása az AKS-sel][aks-aad]című témakörben található.

## <a name="use-role-based-access-controls-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) használata

**Ajánlott eljárások –** A Kubernetes RBAC segítségével határozza meg a felhasználók vagy csoportok a fürt erőforrásaihoz szükséges engedélyeket. Hozzon létre olyan szerepköröket és kötéseket, amelyek a legkevesebb szükséges engedélyeket rendelik hozzá. Integrálja az Azure AD-vel, hogy a felhasználói állapot vagy a csoporttagság bármilyen változása automatikusan frissüljön, és a fürterőforrásokhoz való hozzáférés aktuális.

A Kubernetes, a fürt erőforrásaihoz való hozzáférés részletes vezérlését biztosíthatja. Az engedélyek a fürt szintjén vagy adott névterekben definiálhatók. Megadhatja, hogy mely erőforrások kezelhetők, és milyen engedélyekkel. Ezek a szerepkörök ezután kötéssel rendelkező felhasználókra vagy csoportokra vonatkoznak. A *szerepkörökről,* *a fürtözhelyekről*és *a kötésekről*az [Azure Kubernetes-szolgáltatás (AKS) Hozzáférési és identitáskezelési beállításai című][aks-concepts-identity]témakörben talál további információt.

Például létrehozhat egy szerepkört, amely teljes hozzáférést biztosít az erőforrásokhoz a *finance-app*nevű névtérben, ahogy az a következő példa YAML-jegyzékfájlban látható:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Ezután létrejön egy RoleBinding, amely az Azure AD-felhasználó *fejlesztőjét1\@contoso.com* a RoleBindinghez köti, ahogy az a következő YAML-jegyzékfájlban látható:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Ha *a\@developer1 contoso.com* az AKS-fürtön van hitelesítve, teljes körű engedélyekkel rendelkeznek a *pénzügyi alkalmazás* névterén lévő erőforrásokhoz. Ily módon logikailag elkülönítheti és szabályozhatja az erőforrásokhoz való hozzáférést. Kubernetes RBAC együtt kell használni az Azure AD-integráció, az előző szakaszban tárgyalt.

Ha meg szeretné tekinteni, hogyan használhatja az Azure AD-csoportokat a Kubernetes-erőforrásokhoz való hozzáférés rBAC használatával történő vezérléséhez, olvassa [el a fürterőforrásokhoz való hozzáférés szabályozása szerepköralapú hozzáférés-vezérléssel és az Azure Active Directory-identitások használatával az AKS-ben című témakört.][azure-ad-rbac]

## <a name="use-pod-identities"></a>Pod-identitások használata

**Ajánlott eljárásokra vonatkozó útmutatás** – ne használjon rögzített hitelesítő adatokat a podokon vagy a tárolórendszerképeken belül, mivel az expozíció vagy a visszaélés veszélye fenyegeti őket. Ehelyett pod identitások használatával automatikusan hozzáférést kér egy központi Azure AD-identitáskezelési megoldás használatával. Pod identitások csak Linux podok és tárolórendszerképek használatra.

Ha a podok más Azure-szolgáltatásokhoz, például a Cosmos DB-hez, a Key Vaulthoz vagy a Blob Storage-hoz való hozzáférésre van szükségük, a podnak hozzáférési hitelesítő adatokra van szüksége. Ezek a hozzáférési hitelesítő adatok definiálhatók a tárolórendszerképpel, vagy Kubernetes-titkos adatként injektálhatók, de manuálisan kell létrehozni és hozzárendelni. Gyakran a hitelesítő adatok újra felvannak használva a podok között, és nem rendszeresen forgatni.

Felügyelt identitások az Azure-erőforrások (jelenleg egy társított AKS nyílt forráskódú projektként valósítva meg) segítségével automatikusan hozzáférést kérhet a szolgáltatásokhoz az Azure AD-n keresztül. Nem határozza meg manuálisan a podok hitelesítő adatait, ehelyett valós időben kérnek egy hozzáférési jogkivonatot, és csak a hozzárendelt szolgáltatások eléréséhez használhatják. Az AKS-ben a fürtoperátor két összetevőt telepít fel, hogy a podok számára lehetővé tegyék a felügyelt identitások használatát:

* **A csomópont-felügyeleti identitás (NMI) kiszolgáló** egy pod, amely daemonset ként fut az AKS-fürt minden csomópontján. Az NMI-kiszolgáló figyeli az Azure-szolgáltatások pod-kérelmeket.
* **A felügyelt identitásvezérlő (MIC)** egy központi pod, amely rendelkezik a Kubernetes API-kiszolgáló lekérdezésére vonatkozó engedélyekkel, és egy podnak megfelelő Azure-identitás-leképezést keres.

Amikor a podok hozzáférést kérnek egy Azure-szolgáltatáshoz, a hálózati szabályok átirányítják a forgalmat a csomópontkezelési identitás (NMI) kiszolgálóra. Az NMI-kiszolgáló azonosítja azokat a podokat, amelyek távoli címük alapján hozzáférést kérnek az Azure-szolgáltatásokhoz, és lekérdezi a felügyelt identitásvezérlőt (MIC). A MIC ellenőrzi az Azure-identitás-leképezések az AKS-fürtben, és az NMI-kiszolgáló majd kér egy hozzáférési jogkivonatot az Azure Active Directory (AD) alapján a pod identitás-leképezés. Az Azure AD hozzáférést biztosít az NMI-kiszolgálóhoz, amely visszakerül a podhoz. Ezt a hozzáférési jogkivonatot a pod használhatja, majd hozzáférést kérhet az Azure-beli szolgáltatásokhoz.

A következő példában egy fejlesztő létrehoz egy podot, amely felügyelt identitást használ egy Azure SQL Server-példány hoz való hozzáférés hez:

![Pod identitások lehetővé teszik a pod automatikusan hozzáférést kér más szolgáltatások](media/operator-best-practices-identity/pod-identities.png)

1. A fürtoperátor először létrehoz egy szolgáltatásfiókot, amely az identitások leképezésére használható, amikor a podok hozzáférést kérnek a szolgáltatásokhoz.
1. Az NMI-kiszolgáló és a MIC telepítve vannak a hozzáférési jogkivonatokpodon lévő kérelmek továbbítására az Azure AD-nek.
1. A fejlesztő telepíti a pod egy felügyelt identitás, amely hozzáférési jogkivonatot kér az NMI-kiszolgálón keresztül.
1. A jogkivonat visszakerül a pod, és egy Azure SQL Server-példány eléréséhez használható.

> [!NOTE]
> Felügyelt pod identitások egy nyílt forráskódú projekt, és nem támogatja az Azure technikai támogatás.

Pod-identitások használatához tekintse meg az [Azure Active Directory-identitások a Kubernetes-alkalmazásokhoz.][aad-pod-identity]

## <a name="next-steps"></a>További lépések

Ez az ajánlott eljárások a fürt és az erőforrások hitelesítésére és engedélyezésére összpontosít. Az ajánlott eljárások némelyikének megvalósításához olvassa el az alábbi cikkeket:

* [Az Azure Active Directory integrálása az AKS-sel][aks-aad]
* [Felügyelt identitások használata az Azure-erőforrásokhoz az AKS-sel][aad-pod-identity]

Az AKS fürtműveleteiről az alábbi gyakorlati tanácsokban talál további információt:

* [Több-bérlős működés és fürtelkülönítés][aks-best-practices-cluster-isolation]
* [Egyszerű Kubernetes ütemező funkciók][aks-best-practices-scheduler]
* [Speciális Kubernetes ütemező funkciók][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
