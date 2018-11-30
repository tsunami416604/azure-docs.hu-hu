---
title: Ajánlott eljárások operátor - identitás az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a fürt operátor gyakorlati tanácsok a hogyan kezelheti a hitelesítési és engedélyezési fürtök az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 478034d1c9f99f40a4827515433357c76235e9ee
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428941"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Hitelesítés és engedélyezés az Azure Kubernetes Service (AKS) ajánlott eljárásai

Üzembe helyezése és karbantartása a fürtöket az Azure Kubernetes Service (AKS), és -szolgáltatások erőforrásaihoz való hozzáférés kezelésének módjai megvalósításához szüksége. Ezek a vezérlők nélkül fiókok rendelkezhetnek erőforrásokat és szolgáltatásokat, nincs szükségük a hozzáférést. Azt is lehet visszakövetését, hogy mely hitelesítő adatok készletét is végre a módosításokat.

Hogyan kezelheti egy fürt operátor a hozzáférési és azonosító AKS-fürtök esetén a – gyakorlati tanácsok cikk összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az Azure Active Directory az AKS-fürt felhasználók hitelesítése
> * Szerepköralapú hozzáférés-vezérlést (RBAC) az erőforrásokhoz való hozzáférés vezérlése
> * Egy felügyelt identitás használatával hitelesítik magukat más szolgáltatásokkal

## <a name="use-azure-active-directory"></a>Az Azure Active Directory használatával

**Ajánlott eljárásokkal kapcsolatos útmutatás** -üzembe AKS-fürt az Azure AD-integráció a. Az Azure AD-vel központosítja az identity management összetevő. Bármely felhasználói fiók vagy csoport állapotának változása automatikusan frissül a hozzáférést az AKS-fürtöt. Használja a szerepkörök vagy ClusterRoles és -kötések, hatókör felhasználóknak vagy csoportoknak a lehető legkevesebb szükséges engedélyekkel rendelkezik a következő szakaszban leírt módon.

A fejlesztők és a egy Kubernetes-fürtöt az alkalmazástulajdonosok különböző erőforrásokhoz való hozzáférésre van szükségük. Kubernetes-szabályozható, hogy mely felhasználók mely erőforrásaival is interakcióba identitáskezelési megoldás nem biztosít. Ehelyett általában a fürt és az integrálása egy meglévő identitáskezelési megoldás. Az Azure Active Directory (AD) egy nagyvállalati szintű identitáskezelési megoldást biztosít, és az AKS-fürtökkel integrálva.

Az Azure Active Directoryba integrált fürtökhöz az aks-ben, létrehozhat *szerepkörök* vagy *ClusterRoles* definiáló hozzáférési engedélyeket az erőforrásokhoz. Ezt követően *kötési* a szerepkörök, felhasználók vagy csoportok Azure AD-ből. A Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC) a következő szakasz tárgyalja. Az integráció az Azure AD és az erőforrásokhoz való hozzáférés szabályozását az alábbi ábrán láthatók:

![Fürt-szintű hitelesítést az Azure Active Directory-integráció az aks-sel](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Fejlesztői az Azure AD hitelesíti.
1. Az Azure AD-kiállítási végpont kiállítja a hozzáférési jogkivonatot.
1. A fejlesztői végrehajt egy műveletet, az Azure AD-jogkivonat, például használatával `kubectl create pod`
1. Kubernetes érvényesíti a jogkivonatot az Azure Active Directoryval, és beolvassa a fejlesztői csoporttagságok.
1. Kubernetes szerepköralapú hozzáférés-vezérlési (RBAC) és a fürt szabályzatokkal.
1. Fejlesztői kérelme, mert sikeres, illetve nem az előző érvényesítése az Azure AD biztonságicsoport-tagság és a Kubernetes RBAC és a szabályzatok alapján.

Az Azure AD használó egy AKS-fürt létrehozása: [integrálása az Azure Active Directory az aks-sel][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Szerepköralapú hozzáférés-vezérlést (RBAC) használata

**Ajánlott eljárásokkal kapcsolatos útmutatás** -használata Kubernetes rbac-t a fürt erőforrásaihoz, felhasználók vagy csoportok engedélyeinek megadásához. Hozzon létre a szerepkörök és a kötéseket, hogy a lehető legkevesebb szükséges engedélyek hozzárendelése. Az Azure AD integrálása a felhasználói állapot vagy a csoporttagság bármilyen módosítása automatikusan frissül, és fürt-erőforrásokhoz való hozzáférés jelenlegi.

A Kubernetes szabályozhatja a hozzáférést a fürtben lévő erőforrásokat biztosíthat. Engedélyek a fürt szintjén, vagy az alkalmazásspecifikus névtereket lehet definiálni. Megadhatja, milyen erőforrások is kezelhetők, és milyen engedélyekkel. Ezek a szerepkörök felelnek a alkalmazott felhasználókhoz vagy csoportokhoz a kötés. További információ *szerepkörök*, *ClusterRoles*, és *kötések*, lásd: [hozzáférési és azonosító beállításai az Azure Kubernetes Service (AKS)] [aks-concepts-identity].

Például, létrehozhat egy szerepkör, amely teljes hozzáférést biztosít az erőforrásokhoz nevű névtér *pénzügyi alkalmazások*, ahogyan az a következő példa YAML jegyzékfájl:

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

Egy RoleBinding majd jön létre, amely összeköti az Azure AD-felhasználó *developer1@contoso.com* , a RoleBinding, ahogyan az a következő YAML-jegyzékfájl:

```yaml
ind: RoleBinding
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

Amikor *developer1@contoso.com* hitelesítése szemben az AKS-fürtöt, az erőforrásokhoz való teljes körű engedélyekkel rendelkeznek a *pénzügyi alkalmazások* névtér. Ez egyébként logikailag elkülönítve, és a vezérlő elérhető erőforrások. Kubernetes RBAC az Azure-ral összefüggésben használandó AD-integráció, az előző szakaszban leírt módon.

## <a name="use-pod-identities"></a>Használja a pod-identitások

**Ajánlott eljárásokkal kapcsolatos útmutatás** – ne használja a podok vagy tárolórendszerképek belül a rögzített hitelesítő adatokat, azok fennáll a kockázata, kitettség használatra vagy visszaélésre. Ehelyett használja a pod identitások használva automatikusan kéri a központi hozzáférés az Azure AD identitáskezelési megoldás.

Ha a podok más Azure-szolgáltatásokhoz, például a Cosmos DB, a Key Vault vagy a Blob Storage-ban való hozzáférésre van szükségük a pod kell eléréséhez szükséges hitelesítő adatokat. A hozzáférési hitelesítő adatokat akkor beszélünk, ha a tároló rendszerképét az vagy egy Kubernetes-titokként alkalmazza, de kell manuálisan létrehozott és hozzárendelt. Gyakran előfordul a hitelesítő adatok között podok újra felhasználhatók, és nem rendszeresen elforgatva.

Felügyelt identitások az Azure-erőforrások segítségével automatikusan az Azure AD-szolgáltatásokhoz való hozzáférés kéréséhez. Nem manuális megadása között hitelesítő adatokat a podok, ehelyett azokat egy hozzáférési jogkivonat, valós idejű, és az csak a hozzárendelt szolgáltatások eléréséhez használható. Az aks-ben két összetevőből, hogy a podok felügyelt identitásokat használ a fürt operátor által telepített:

* **A csomópont felügyeleti identitás (NMI) kiszolgáló** van, amely egy DaemonSet az AKS-fürt minden csomópontján fut egy pod. A NMI kiszolgáló figyel a pod-kéréseket az Azure-szolgáltatások.
* **A felügyelt identitás vezérlő (Mikrofon)** központi podot engedélyekkel a Kubernetes API-kiszolgáló lekérdezéséhez, és keres egy Azure-identitás leképezés, amely egy pod felel meg.

Podok kérhet hozzáférést az Azure-szolgáltatások, ha a hálózati szabályok a forgalom átirányítása a csomópont felügyeleti identitás (NMI) kiszolgálóhoz. A NMI kiszolgáló, amely az Azure-szolgáltatásokhoz való hozzáférés a távoli cím alapján, és lekérdezi a felügyelt identitás vezérlő (Mikrofon) podok azonosítja. A MIC ellenőrzi az AKS-fürtöt, és a NMI kiszolgáló az Azure-identitás leképezések, majd a pod identitásleképezési alapján az Azure Active Directory (AD) hozzáférési jogkivonatot kér. Azure ad-ben a NMI kiszolgálóra, amely a rendszer visszaadja a pod hozzáférést biztosít. Ez a jogkivonat segítségével által a pod lekérhetik az Azure-szolgáltatásokhoz való hozzáférés.

A következő példában egy fejlesztői igényelhet hozzáférést egy Azure SQL Server-példány egy felügyelt identitás használó podot hoz létre:

![Podok identitások podot használva automatikusan kéri az egyéb szolgáltatásokhoz való hozzáférés engedélyezése](media/operator-best-practices-identity/pod-identities.png)

1. Fürt üzemeltető először használható identitások leképezésére podok szolgáltatásokhoz való hozzáférés kérése szolgáltatásfiók hoz létre.
1. Az Azure AD hozzáférési jogkivonatokat pod kérések továbbítási a NMI kiszolgáló és a MIC telepített.
1. A fejlesztő üzembe helyezi egy felügyelt identitás, amely a NMI kiszolgálón keresztül egy hozzáférési jogkivonatot kér rendelkező podot.
1. A jogkivonat a pod vissza, és a egy Azure SQL Server-példány eléréséhez használt.

A pod-identitásokat használ, tekintse meg a [Kubernetes-alkalmazások Azure Active Directory identitásainak][aad-pod-identity].

## <a name="next-steps"></a>További lépések

Ez – gyakorlati tanácsok cikk hitelesítését és engedélyezését a fürt és az erőforrások összpontosít. Néhány ajánlott eljárások végrehajtásához a következő cikkekben talál:

* [Azure Active Directory integrálása az aks-sel][aks-aad]
* [Felügyelt identitások használata az Azure-erőforrásokhoz az aks-sel][aad-pod-identity]

Az AKS-fürt műveletekkel kapcsolatos további információkért tekintse meg az alábbi gyakorlati tanácsokat:

* [Több-bérlős és a fürt elkülönítés][aks-best-practices-scheduler]
* [Kubernetes-ütemezési alapszintű funkciók][aks-best-practices-scheduler]
* [Kubernetes-ütemező speciális funkciók][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: aad-integration.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
