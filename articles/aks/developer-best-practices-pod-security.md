---
title: Pod biztonsági gyakorlati tanácsok
titleSuffix: Azure Kubernetes Service
description: Ismerje meg a fejlesztői gyakorlati tanácsok, hogyan biztonságos podok az Azure Kubernetes szolgáltatás (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804384"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) pod-biztonsággal kapcsolatos gyakorlati tanácsok

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazások fejlesztése és futtatása során a podok biztonsága kulcsfontosságú szempont. Az alkalmazásokat a szükséges jogosultságok minimális számának elvére kell megtervezni. A személyes adatok biztonságának megőrzése az ügyfelek számára a legfontosabb. Nem szeretné, hogy a hitelesítő adatok, például az adatbázis-kapcsolati karakterláncok, kulcsok vagy titkos kulcsok és tanúsítványok elérhetők legyenek a külvilág számára, ahol a támadó rosszindulatú célokra kihasználhatja ezeket a titkokat. Ne adja hozzá őket a kódhoz, és ne ágyazza be őket a tárolórendszerképekbe. Ez a megközelítés kockázatot hozna létre az expozíció, és korlátozza a hitelesítő adatok elforgatásának lehetőségét, mivel a tárolórendszerképek et újra kell építeni.

Ez az ajánlott eljárások cikk az AKS-ben a podok biztonságossá tétele. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Pod biztonsági környezet használata a folyamatokhoz és szolgáltatásokhoz való hozzáférés vagy a jogosultságok eszkalációjának korlátozására
> * Hitelesítés más Azure-erőforrásokkal pod által felügyelt identitások használatával
> * Hitelesítő adatok kérése és lekérése egy digitális tárolóból, például az Azure Key Vaultból

A [fürtbiztonsággal][best-practices-cluster-security] és a [tárolórendszerkép-kezeléssel][best-practices-container-image-management]kapcsolatos gyakorlati tanácsokat is elolvashatja.

## <a name="secure-pod-access-to-resources"></a>Biztonságos pod hozzáférés az erőforrásokhoz

**Ajánlott eljárások –** Ha egy másik felhasználóként vagy csoportban szeretne futni, és korlátozza a hozzáférést az alapul szolgáló csomópontfolyamatokhoz és -szolgáltatásokhoz, adja meg a pod biztonsági környezetbeállításait. Rendelje hozzá a szükséges jogosultságok közül a legkevesebbet.

Ahhoz, hogy az alkalmazások megfelelően fussanak, a podok nak meghatározott felhasználóként vagy csoportként kell futniuk, nem *pedig gyökérként.* A `securityContext` pod vagy tároló lehetővé teszi, hogy adja meg a beállításokat, például *runAsUser* vagy *fsGroup* a megfelelő engedélyeket. Csak rendelje hozzá a szükséges felhasználói vagy csoportengedélyeket, és ne használja a biztonsági környezetet további engedélyek átvállalására. A *runAsUser*, jogosultságeszkaláció, és más Linux-képességek beállításai csak Linux csomópontokon és podokon érhetők el.

Ha nem legfelső szintű felhasználóként fut, a tárolók nem köthetők az 1024 alatti kiemelt portokhoz. Ebben a forgatókönyvben a Kubernetes-szolgáltatások segítségével leplezheti azt a tényt, hogy egy alkalmazás egy adott porton fut.

A pod biztonsági környezetben is definiálhat további képességek et vagy engedélyeket a folyamatok és szolgáltatások eléréséhez. A következő általános biztonsági környezetdefiníciók állíthatók be:

* **az allowPrivilegeEscalation** határozza meg, hogy a pod átvehet-e *gyökérjogosultságokat.* Úgy tervezze meg az alkalmazásokat, hogy ez a beállítás mindig *hamis*beállításra van állítva.
* **A Linux-képességek** lehetővé teszik, hogy a pod hozzáférést ad a csomópont folyamatok alapjául szolgáló folyamatok. Ügyeljen arra, hogy ezeket a képességeket hozzárendeli. Rendelje hozzá a szükséges jogosultságok közül a legkevesebbet. További információ: [Linux-képességek][linux-capabilities].
* **Az SELinux-címkék** egy Linux kernel biztonsági modul, amely lehetővé teszi a szolgáltatások, folyamatok és fájlrendszer-hozzáférés hozzáférési szabályzatainak meghatározását. Ismét rendelje hozzá a szükséges jogosultságok legkevesebb számát. További információ: [SELinux-beállítások a Kubernetes-ben][selinux-labels]

A következő példa pod YAML manifest határozza meg a biztonsági környezet beállításait:

* A pod *1000-es* azonosítós azonosítóként és a *2000-es* csoportazonosító részeként fut
* Nem lehet elévítani a használatra való jogosultságokat`root`
* Lehetővé teszi a Linux-képességek számára a hálózati interfészek és a gazdagép valós idejű (hardveres) órájának elérését

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

A fürtkezelővel együttműködve határozza meg, hogy milyen biztonsági környezetbeállításokra van szüksége. Próbálja meg megtervezni az alkalmazásokat a további engedélyek minimalizálása és a pod szükséges eléréséhez. Az AppArmor és a seccomp (secure computing) használatával további biztonsági funkciók kal korlátozhatók, amelyeket a fürtkezelők megvalósíthatnak. További információ: [Biztonságos tároló az erőforrásokhoz való hozzáférés.][apparmor-seccomp]

## <a name="limit-credential-exposure"></a>Hitelesítő adatok expozíciójának korlátozása

**Ajánlott eljárások –** ne adja meg a hitelesítő adatokat az alkalmazáskódban. Felügyelt identitások azure-erőforrások segítségével a pod hozzáférést kérhet más erőforrásokhoz. A digitális trezor, például az Azure Key Vault, is kell használni a digitális kulcsok és hitelesítő adatok tárolására és lekérésére. Pod felügyelt identitások csak Linux-podok és tárolórendszerképek használatra készült.

Az alkalmazáskódban a hitelesítő adatok felfedésének kockázatának csökkentése érdekében kerülje a rögzített vagy megosztott hitelesítő adatok használatát. A hitelesítő adatokat vagy kulcsokat nem szabad közvetlenül a kódban megadni. Ha ezek a hitelesítő adatok elérhetővé, az alkalmazást frissíteni kell, és újratelepíteni kell. Egy jobb megközelítés az, hogy podok saját identitását és a módját, hogy hitelesítsék magukat, vagy automatikusan lekérni a hitelesítő adatokat egy digitális tárolóból.

A következő [társított AKS nyílt forráskódú projektek][aks-associated-projects] lehetővé teszik a podok automatikus hitelesítését, vagy hitelesítő adatok és kulcsok kérése egy digitális tárolóból:

* Felügyelt identitások az Azure-erőforrásokhoz, és
* Azure Key Vault FlexVol illesztőprogram

Társított AKS nyílt forráskódú projektek nem támogatja az Azure technikai támogatás. Azért biztosítjuk őket, hogy visszajelzéseket és hibákat gyűjtsenek a közösségünktől. Ezek a projektek nem ajánlottak éles használatra.

### <a name="use-pod-managed-identities"></a>Pod felügyelt identitások használata

Az Azure-erőforrások felügyelt identitása lehetővé teszi, hogy egy pod hitelesítse magát az azt támogató Azure-szolgáltatásokkal, például a Storage-ral vagy az SQL-vel szemben. A pod van rendelve egy Azure-identitás, amely lehetővé teszi számukra, hogy hitelesítse magát az Azure Active Directoryban, és kap egy digitális jogkivonatot. Ez a digitális jogkivonat más Azure-szolgáltatások számára is megjeleníthető, amelyek ellenőrzik, hogy a pod jogosult-e a szolgáltatás elérésére és a szükséges műveletek végrehajtására. Ez a megközelítés azt jelenti, hogy például az adatbázis-kapcsolati karakterláncok nem szükségesek titkos kulcsokat. A pod által felügyelt identitás egyszerűsített munkafolyamata az alábbi ábrán látható:

![Egyszerűsített munkafolyamat az Azure podfelügyelt identitásához](media/developer-best-practices-pod-security/basic-pod-identity.png)

Felügyelt identitás, az alkalmazáskód nem kell megadnia a hitelesítő adatokat egy szolgáltatás, például az Azure Storage eléréséhez. Mivel minden pod saját identitással hitelesíti magát, így naplózhatja és megtekintheti a hozzáférést. Ha az alkalmazás más Azure-szolgáltatásokkal csatlakozik, felügyelt identitások használatával korlátozhatja a hitelesítő adatok újrafelhasználását és az expozíció kockázatát.

A pod-identitásokról további információt az [AKS-fürt konfigurálása pod felügyelt identitások használatára és az alkalmazásokkal című][aad-pod-identity] témakörben talál.

### <a name="use-azure-key-vault-with-flexvol"></a>Az Azure Key Vault használata a FlexVol segítségével

A felügyelt podidentitások nagyszerűen működnek az Azure-szolgáltatások támogatása érdekében. A saját szolgáltatások vagy alkalmazások felügyelt identitások nélkül az Azure-erőforrások, továbbra is hitelesítő adatok kal vagy kulcsok használatával hitelesíti. A digitális trezor tárolhatja ezeket a hitelesítő adatokat.

Ha az alkalmazásoknak hitelesítő adatokra van szükségük, kommunikálnak a digitális tárolóval, lekérik a legújabb hitelesítő adatokat, majd csatlakoznak a szükséges szolgáltatáshoz. Az Azure Key Vault lehet ez a digitális tároló. Az egyszerűsített munkafolyamat a hitelesítő adatok beolvasása az Azure Key Vault pod felügyelt identitások pod felügyelt identitások látható az alábbi ábrán:

![Egyszerűsített munkafolyamat a hitelesítő adatok key vaultból történő beolvasásához egy pod által felügyelt identitás használatával](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

A Key Vault segítségével tárolja és rendszeresen elforgatja a titkos kulcsokat, például a hitelesítő adatokat, a tárfiók kulcsokat vagy a tanúsítványokat. Az Azure Key Vault integrálható egy AKS-fürt segítségével FlexVolume. A FlexVolume illesztőprogram lehetővé teszi, hogy az AKS-fürt natív módon lekérje a hitelesítő adatokat a Key Vaultból, és biztonságosan csak a kérelmező pod számára biztosítsa azokat. Együttműködve a fürt üzemeltetőjével a Key Vault FlexVol illesztőprogramjának az AKS-csomópontokra való üzembe helyezéséhez. Pod felügyelt identitás használatával hozzáférést kérhet a Key Vaulthoz, és lekérheti a szükséges hitelesítő adatokat a FlexVolume illesztőprogramon keresztül.

Az Azure Key Vault és a FlexVol linuxos podokon és csomópontokon futó alkalmazások hoz való használatra készült.

## <a name="next-steps"></a>További lépések

Ez a cikk a podok védelmére összpontosított. Az alábbi témakörcikkekben az alábbi cikkekben valósíthatja meg a területeket:

* [Felügyelt identitások használata az Azure-erőforrásokhoz az AKS-sel][aad-pod-identity]
* [Az Azure Key Vault integrálása az AKS-sel][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
