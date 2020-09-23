---
title: Adatkezelő létrehozása Azure Data Studioban
description: Adatkezelő létrehozása Azure Data Studioban
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71678977f899b910a97dbb552233f36ca5a51f26
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939996"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Adatkezelő létrehozása Azure Data Studioban

Azure Data Studio használatával létrehozhat egy adatvezérlőt a központi telepítés varázsló és a jegyzetfüzetek segítségével.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

- Hozzá kell férnie egy Kubernetes-fürthöz, és a kubeconfig-fájl úgy van konfigurálva, hogy a telepíteni kívánt Kubernetes-fürtre mutasson.
- [Telepítenie kell az ügyféleszközök](install-client-tools.md) **Azure Data Studio** az **Azure arc** és az azure-beli **adatcli**nevű Azure Data Studio-bővítményeket is.
- Azure Data Studio-ban be kell jelentkeznie az Azure-ba.  Ehhez írja be a CTRL/Command + SHIFT + P billentyűkombinációt a parancs szövege ablak megnyitásához, majd írja be a következőt: **Azure**.  Válassza **Az Azure: bejelentkezés**lehetőséget.   Egy Azure-fiók hozzáadásához kattintson a jobb felső sarokban található + ikonra a panelen.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Az Azure arc-adatkezelő létrehozása a központi telepítési varázslóval

Az alábbi lépéseket követve létrehozhat egy Azure arc-adatkezelőt a központi telepítés varázsló segítségével.

1. A Azure Data Studio kattintson a bal oldali navigációs sávon a kapcsolatok fülre.
2. Kattintson a kapcsolatok panel tetején található **...** gombra, és válassza az **új központi telepítés...** lehetőséget.
3. Az új központi telepítés varázslóban válassza az **Azure arc-adatkezelő**elemet, jelölje be a licenc-elfogadás jelölőnégyzetet, majd kattintson a lap alján található **kiválasztás** gombra.
4. Használja az alapértelmezett kubeconfig-fájlt, vagy válasszon egy másikat.  Kattintson a **Tovább** gombra.
5. Válassza ki a Kubernetes-fürt környezetét. Kattintson a **Tovább** gombra.
6. A cél Kubernetes-fürttől függően válasszon egy telepítési konfigurációs profilt tartalmazó fájlt. **Kattintson a Tovább gombra**.
8. Válassza ki a kívánt előfizetést és erőforráscsoportot.
9. Adja meg az adatvezérlő nevét, valamint azt a névteret, amelyhez az adatkezelőt létre kívánja hozni.  

> [!NOTE]
> Ha a névtér már létezik, akkor a rendszer akkor fogja használni, ha a névtér még nem tartalmaz más Kubernetes objektumokat – hüvelyeket stb.  Ha a névtér nem létezik, a rendszer kísérletet tesz a névtér létrehozására.  A névtér Kubernetes-fürtben való létrehozásához a Kubernetes-fürt rendszergazdai jogosultságai szükségesek.  Ha nem rendelkezik Kubernetes-fürt rendszergazdai jogosultságokkal, kérje meg a Kubernetes-fürt rendszergazdáját, hogy hajtsa végre az első néhány lépést az [adatkezelő létrehozása a Kubernetes natív eszközökkel](./create-data-controller-using-k8s-native-tools.md) című cikkben, amelyet egy Kubernetes-rendszergazdának kell elvégeznie a varázsló befejezése előtt.

> [!NOTE]
> Megjegyzés: a Kubernetes-fürtben az adatkezelő és a névtér neve alapján hozhat létre egyéni erőforrást, így meg kell felelnie a [Kubernetes elnevezési konvencióknak](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Válasszon egy Azure-beli helyet.
   
> [!NOTE]
> Az itt kiválasztott Azure-hely az az Azure-beli hely, ahol az adatvezérlővel és az általa kezelt adatbázis-példányokkal kapcsolatos *metaadatok* lesznek tárolva.  Az adatkezelő és az adatbázis példányai ténylegesen crewted lesznek a Kubernetes-fürtben, bárhol is legyenek.

11.  Adja meg a felhasználónevet és a jelszót, és erősítse meg az adatkezelő rendszergazdai felhasználói fiókjának jelszavát.

> [!NOTE]
> A jelszónak legalább 8 karakter hosszúnak kell lennie.

1.  Kattintson a **Tovább** gombra.
2.  Tekintse át **a parancsfájlt, és kattintson a jegyzetfüzetre**.
3.  **Tekintse át a generált jegyzetfüzetet**.  Végezze el a szükséges módosításokat, például a tárolási osztályok nevét vagy a szolgáltatási típusokat.
4.  Kattintson az **összes futtatása** gombra a jegyzetfüzet tetején.

## <a name="monitoring-the-creation-status"></a>A létrehozási állapot figyelése

A vezérlő létrehozása több percet is igénybe vehet. A következő parancsokkal figyelheti a folyamatot egy másik terminál ablakban:

> [!NOTE]
>  Az alábbi parancsok azt feltételezik, hogy létrehozott egy adatkezelőt és egy Kubernetes-névteret az "arc" névvel.  Ha más névtér-vagy adatvezérlő-nevet használt, az "ív" kifejezés helyére a nevét használhatja.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Egy adott Pod létrehozási állapotát is megtekintheti egy, az alábbihoz hasonló parancs futtatásával.  Ez különösen hasznos az esetleges problémák elhárításához.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>A létrehozási problémák elhárítása

Ha bármilyen problémába ütközik a létrehozással kapcsolatban, tekintse meg a [hibaelhárítási útmutatót](troubleshoot-guide.md).
