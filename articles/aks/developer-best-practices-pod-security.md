---
title: Ajánlott eljárások Developer - Pod biztonság az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg, hogyan védheti meg az Azure Kubernetes Service (AKS) podok fejlesztői ajánlott eljárásai
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: eaeb81d7f93124f1f3dedf9676314b1b786d8571
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117791"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (AKS) pod biztonsági védelmének bevált gyakorlata

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a podok biztonságát kulcs veszi figyelembe. Az alkalmazásokat úgy kell megtervezni, hogy a lehető legkevesebb jogosultsággal rendelkezzenek. Felső részén szem előtt az ügyfeleknek titkos adatok biztonságos kezelése mellett. Nem szeretné, hogy a hitelesítő adatok, például az adatbázis-kapcsolati karakterláncok, kulcsok, titkos kódok és a külvilág számára elérhető tanúsítványok, ahol a támadók kihasználhatják ezeket a titkokat rosszindulatú célokra. Nem hozzáadhatja őket a kód, vagy a tárolólemezképek beágyazása őket. Ezt a módszert akkor hozzon létre egy kockázatát mérik a különböző és elforgatásának ezeket a hitelesítő adatokat, mint a tárolórendszerképeket építhető újra kell korlátozni.

Ez az ajánlott eljárási cikk a hüvelyek az AK-ban való védelmére összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Folyamatok és a szolgáltatások vagy a jogosultságok eszkalálását való hozzáférés korlátozására használja a pod biztonsági környezet
> * Hitelesítés a többi Azure-erőforrások pod felügyelt identitások használatával
> * Kérelem és lekérni egy digitális például az Azure Key Vault-tárolót a hitelesítő adatokat

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [tárolók rendszerképének kezelésével][best-practices-container-image-management]kapcsolatos ajánlott eljárásokat is.

## <a name="secure-pod-access-to-resources"></a>Podok erőforrásokhoz való hozzáférés biztonságossá tétele

**Ajánlott eljárási útmutató** – egy másik felhasználóként vagy csoportként való futtatásra, valamint a mögöttes csomópont-folyamatok és-szolgáltatások elérésének korlátozására, a pod biztonsági környezet beállításainak megadása. Rendelje hozzá a legkevésbé szükséges jogosultságok száma.

Ahhoz, hogy az alkalmazások megfelelően fussanak, a hüvelyeket definiált felhasználóként vagy csoportként kell futtatni, és nem *root*-ként. A pod vagy a Container `securityContext` lehetővé teszi olyan beállítások megadását, mint például a *runAsUser* vagy a *fsGroup* , hogy a megfelelő engedélyeket tegyük fel. Csak rendelni a szükséges felhasználói vagy biztonságicsoport-engedélyeit, és ne használja a biztonsági környezet eszközeként tegyük fel, további engedélyeket. A *runAsUser*, a jogosultság-eszkaláció és az egyéb linuxos képességek beállításai csak Linux-csomópontokon és hüvelyeken érhetők el.

Futtatásakor nem legfelső szintű felhasználóként, tárolók nem köthető a rendszerjogosultságú portokat az 1024. Ebben a forgatókönyvben a Kubernetes-szolgáltatás segítségével álcázza a tény, hogy egy alkalmazás fut, egy adott portot.

A pod biztonsági környezet is megadhatja, további funkciókat, vagy a folyamatok és szolgáltatások eléréséhez szükséges engedélyekkel. A következő gyakori biztonsági környezet definíciókat adhatja meg:

* a **allowPrivilegeEscalation** határozza meg, hogy a pod lehet-e *root* jogosultságot feltételezni. Tervezze meg az alkalmazásait, hogy ezt a beállítást mindig *hamis*értékre állítsa.
* A **Linux-funkciók** lehetővé teszik, hogy a pod hozzáférjen az alapul szolgáló csomópont-folyamatokhoz A hozzárendelés, ezek a képességek gondoskodunk. Rendelje hozzá a legkevésbé száma szükséges jogosultságokkal. További információ: Linux- [képességek][linux-capabilities].
* A **SELinux labels** egy Linux kernel biztonsági modul, amely lehetővé teszi hozzáférési szabályzatok definiálását a szolgáltatások, a folyamatok és a fájlrendszer eléréséhez. Újbóli hozzárendelése a legkevésbé száma szükséges jogosultságokkal. További információ: [SELinux-beállítások a Kubernetes-ben][selinux-labels]

A következő példa pod YAML-jegyzékfájl állítja be a biztonsági környezet beállítások meghatározásához:

* A pod a *1000* felhasználói azonosítóként fut, és az *2000* -es azonosítójú csoport tagja.
* Nem lehet kibővíteni a jogosultságokat a `root` használatára
* Lehetővé teszi, hogy a hálózati adapterek és a gazdagép valós idejű (hardver) óra Linux képességek

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

Milyen biztonsági környezet beállításokat kell meghatározni a fürt operátor működnek. Próbálja meg minimálisra csökkentése érdekében további engedélyekkel és hozzáférési a pod igényel az alkalmazások tervezéséhez. Nincsenek további biztonsági funkciókat korlátozni AppArmor és a seccompot (biztonságos computing), amely a fürt operátorok úgy implementálható. További információ: az [erőforrásokhoz való hozzáférés biztonságossá tétele][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Hitelesítő adatok felfedésével korlát

**Ajánlott eljárási útmutató** – ne adjon meg hitelesítő adatokat az alkalmazás kódjában. Az Azure-erőforrások felügyelt identitások használatával lehetővé teszik a pod kérelem hozzáférés az erőforrásokhoz. Egy digitális tároló tartalmazza, például az Azure Key Vaultban kell is használható, tárolására és beolvasására, a digitális kulcsokat és a hitelesítő adatokat. A hüvelyben felügyelt identitások kizárólag Linux-hüvelyekhez és csak a tárolók rendszerképeihez használhatók.

És a kockázatok hitelesítő adatok elérhetővé váljon az alkalmazás kódjában, kerülje a rögzített vagy megosztott hitelesítő adatokat használjanak. Hitelesítő adatokhoz vagy kulcsokhoz nem tartalmaz közvetlenül a kódba. Ezek a hitelesítő adatok érhetők el, ha az alkalmazásnak kell frissíteni, és áttelepült. Jobb módszer, hogy adjon a podok saját identitás- és hitelesíteni magukat, vagy automatikusan lekérni egy digitális tárolót a hitelesítő adatokat.

A következő [társított AK nyílt forráskódú projektek][aks-associated-projects] lehetővé teszik a hüvelyek automatikus hitelesítését, illetve a digitális tárolóban a hitelesítő adatok és kulcsok kérését:

* Felügyelt identitások az Azure-erőforrásokhoz, és
* Az Azure Key Vault FlexVol illesztőprogram

Az Azure technikai támogatási szolgálata nem támogatja a társított AK nyílt forráskódú projektjeit. A felhasználók visszajelzéseket és hibákat biztosítanak a Közösségtől. Ezek a projektek éles használatra nem ajánlottak.

### <a name="use-pod-managed-identities"></a>Használat pod felügyelt identitások

Az Azure-erőforrások felügyelt identitása lehetővé teszi, hogy a pod hitelesítse magát az azt támogató Azure-szolgáltatásokkal, például a Storage vagy az SQL használatával. A pod hozzá van rendelve egy Azure-identitás, amely lehetővé teszi, hogy azokat az Azure Active Directory hitelesítést és a digitális tokent kaphatnak. A digitális token megjeleníthetők az egyéb Azure-szolgáltatások, ellenőrizze, hogy jogosult-e a pod hozzáférhessen a szolgáltatáshoz, és végezze el a szükséges műveleteket. Ez a megközelítés azt jelenti, hogy nincs titkos kódok szükséges adatbázis-kapcsolati karakterláncok, például. A pod felügyelt identitás egyszerűsített munkafolyamatot az alábbi ábrán látható:

![A pod egyszerűsített munkafolyamat felügyelt identitás az Azure-ban](media/developer-best-practices-pod-security/basic-pod-identity.png)

Felügyelt identitással az alkalmazás kódja nem kell tartalmaznia egy szolgáltatáshoz, például az Azure Storage eléréséhez szükséges hitelesítő adatokat. Mivel minden egyes pod hitelesíti magát a saját identitással, így is naplózása és hozzáférés felülvizsgálata. Ha az alkalmazás kapcsolódik az Azure-szolgáltatásokat, használja a felügyelt identitásokból korlát hitelesítő adatok újrafelhasználása és kitettség.

A pod-identitásokkal kapcsolatos további információkért lásd: [AK-fürtök beállítása a pod felügyelt identitások és az alkalmazásai használatára][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Az Azure Key Vault használata FlexVol

Felügyelt pod identitások hitelesítése támogató Azure-szolgáltatások remekül működik. Saját szolgáltatások vagy alkalmazások felügyelt identitások az Azure-erőforrások anélkül hogy továbbra is hitelesítést végezni a hitelesítő adatokhoz vagy kulcsokhoz. Egy digitális tárolót ezen hitelesítő adatok tárolására használható.

Amikor az alkalmazásoknak a hitelesítő adatokat tudnak kommunikálni a digitális tárolóhoz, a legújabb hitelesítő adatok lekéréséhez, és kapcsolódjon a szükséges szolgáltatás. Az Azure Key Vault lehet a digitális tárban. A hitelesítő adatok lekérését az Azure Key Vault pod felügyelt identitások használatával egyszerűsített munkafolyamata az alábbi ábrán látható:

![A hitelesítő adatokat beolvasni a Key Vault használata egy pod egyszerűsített munkafolyamat felügyelt identitás](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

A Key Vault tárolja, és rendszeresen elforgatása a például hitelesítő adatokat, a tárfiók kulcsait vagy a tanúsítványok titkos kulcsok. Az Azure Key Vault integrálható egy FlexVolume használó egy AKS-fürtöt. A FlexVolume illesztőprogram lehetővé teszi, hogy a natív módon olvashatók be hitelesítő adatok a Key Vaultból és biztonságosan azokat csak a kérelmező pod az AKS-fürtöt. A Key Vault FlexVol illesztőprogram be az AKS-csomópontok üzembe helyezéséhez a fürt operátor működnek. A pod felügyelt identitás használatával Key Vault hozzáférés kérése és a hitelesítő adatokat a FlexVolume illesztőprogram keresztül kell lekérni.

A FlexVol-hez készült Azure Key Vault a Linux-hüvelyeken és-csomópontokon futó alkalmazásokkal és szolgáltatásokkal használható.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a podok biztonságossá összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Felügyelt identitások használata Azure-erőforrásokhoz AK-val][aad-pod-identity]
* [Azure Key Vault integrálása AK-val][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
