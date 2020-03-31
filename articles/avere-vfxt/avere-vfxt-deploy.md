---
title: Az Avere vFXT telepítése az Azure-hoz
description: Az Avere vFXT-fürt azure-beli telepítésének lépései
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252596"
---
# <a name="deploy-the-vfxt-cluster"></a>A vFXT-fürt üzembe helyezése

Ez az eljárás végigvezeti az Azure Marketplace-en elérhető üzembe helyezési varázsló használatával. A varázsló automatikusan telepíti a fürtet egy Azure Resource Manager-sablon használatával. Miután megadta a paramétereket az űrlapon, és kattintson a **Létrehozás**gombra, az Azure automatikusan végrehajtja a következő feladatokat:

* Létrehozza a fürtvezérlőt, amely egy alapvető virtuális gép, amely tartalmazza a fürt telepítéséhez és kezeléséhez szükséges szoftvert.
* Beállítja az erőforráscsoportot és a virtuális hálózati infrastruktúrát, beleértve az új elemek létrehozását is.
* Létrehozza a fürtcsomópont virtuális gépeit, és konfigurálja őket Avere-fürtként.
* Ha kérik, létrehoz egy új Azure Blob-tárolót, és fürtmagos fájlkezelőként konfigurálja azt.

A jelen dokumentumban található utasításokat követve egy virtuális hálózat, egy alhálózat, egy fürtvezérlő és egy vFXT-fürt lesz, ahogy az az alábbi ábrán látható. Ez az ábra az opcionális Azure Blob core filer, amely egy új Blob storage-tároló (egy új tárfiókban, nem jelenik meg) és egy szolgáltatás végpont a Microsoft storage az alhálózaton belül.

![három koncentrikus téglalapot ábrázolaz Avere fürtösszetevőivel. A külső téglalap "Erőforráscsoport" címkével van ellátva, és egy hatszögalakú , "Blob storage (optional)" feliratú. A következő téglalap a következő feliratú "Virtuális hálózat: 10.0.0.0/16", és nem tartalmaz semmilyen egyedi összetevőt. A legbelső téglalap "Subnet:10.0.0.0/24" címkével van ellátva, és egy "Fürtvezérlő" címkével ellátott virtuális gépet, három virtuális gépet "vFXT-csomópontok (vFXT-fürt)" és egy "Szolgáltatásvégpont" feliratú hatszöget tartalmaz. Van egy nyíl, amely összeköti a szolgáltatás végpontját (amely az alhálózaton belül van) és a blob storage (amely kívül esik az alhálózaton és a virtuális hálózaton, az erőforráscsoportban). A nyíl áthalad az alhálózaton és a virtuális hálózat határain.](media/avere-vfxt-deployment.png)

A létrehozási sablon használata előtt győződjön meg arról, hogy az alábbi előfeltételeket kezelte:  

* [Új előfizetés](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Előfizetés tulajdonosi engedélyei](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [A vFXT-fürt kvótája](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Tárolási szolgáltatás végpontja (ha szükséges)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – olyan központi telepítésekhez szükséges, amelyek meglévő virtuális hálózatot használnak, és blobtárolót hoznak létre

A fürt telepítési lépéseiről és tervezéséről az [Avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md) és [a telepítés áttekintése című témakörben olvashat bővebben.](avere-vfxt-deploy-overview.md)

## <a name="create-the-avere-vfxt-for-azure"></a>Az Avere vFXT létrehozása az Azure-hoz

A létrehozási sablon elérése az Azure Portalon az Avere keresésével és az "Avere vFXT for Azure ARM Template" kiválasztásával.

![Böngészőablak, amely en az Azure Portalon a "New > Marketplace > Mindent" zsemlemorzsával jeleníti meg. A Minden oldalon a keresési mezőben a "avere" kifejezés szerepel, a második eredmény, az "Avere vFXT for Azure ARM Template" piros sal van körvonalazva, hogy kiemelje azt.](media/avere-vfxt-template-choose.png)

Miután elolvasta az Avere vFXT for Azure ARM Template oldalon a részleteket, kattintson a **Létrehozás** gombra a kezdéshez.

![Az Azure piactér a központi telepítési sablon első oldalán](media/avere-vfxt-deploy-first.png)

A sablon négy lépésből áll - két információgyűjtő oldal, valamint érvényesítési és megerősítési lépések.

* Az első oldal összegyűjti a fürtvezérlő virtuális gépének beállításait.
* A második oldal a fürt létrehozásához szükséges paramétereket és további erőforrásokat, például alhálózatokat és tárolókat gyűjt.
* A harmadik oldal összefoglalja a lehetőségeket, és ellenőrzi a konfigurációt.
* A negyedik oldal ismerteti a szoftver használati feltételeit, és lehetővé teszi a fürt létrehozási folyamatának elindítását.

## <a name="page-one-parameters---cluster-controller-information"></a>Első oldal paraméterei - fürtvezérlő adatai

A központi telepítési sablon első lapja a fürtvezérlőre összpontosít.

![A telepítési sablon első oldala](media/avere-vfxt-deploy-1.png)

Töltse ki a következő adatokat:

* **Fürtvezérlő neve** - Adja meg a fürtvezérlő virtuális gépének nevét.

* **Vezérlő felhasználóneve** - Állítsa be a fürtvezérlő virtuális gépének gyökérfelhasználónevét.

* **Hitelesítés típusa** – Válasszon jelszót vagy SSH nyilvános kulcsú hitelesítést a vezérlőhöz való csatlakozáshoz. Az SSH nyilvános kulcs módszer ajánlott; olvassa [el Az SSH-kulcsok létrehozása és használata,](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ha segítségre van szüksége.

* **Jelszó** vagy **SSH nyilvános kulcs** – A kiválasztott hitelesítési típustól függően meg kell adnia egy RSA nyilvános kulcsot vagy egy jelszót a következő mezőkben. Ez a hitelesítő adat a korábban megadott felhasználónévvel használatos.

* **Előfizetés** – Válassza ki az Avere vFXT előfizetését.

* **Erőforráscsoport** – Válasszon ki egy meglévő üres erőforráscsoportot az Avere vFXT-fürthöz, vagy kattintson az "Új létrehozása" gombra, és adjon meg egy új erőforráscsoport nevet.

* **Hely** – Válassza ki a fürt és az erőforrások Azure-helyét.

Ha **végzett,** kattintson az OK gombra.

> [!NOTE]
> Ha azt szeretné, hogy a fürtvezérlő nyilvános IP-címmel rendelkezjen, hozzon létre egy új virtuális hálózatot a fürt számára, ahelyett, hogy egy meglévő hálózatot választana. Ez a beállítás a második oldalon van.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Második oldal paraméterei - vFXT-fürtinformáció

A központi telepítési sablon második lapján többek között a fürt méretét, a csomópont típusát, a gyorsítótár méretét és a tárolási paramétereket állíthatja be.

![A telepítési sablon második oldala](media/avere-vfxt-deploy-2.png)

* **Avere vFXT fürtcsomópontok száma** – Válassza ki a csomópontok számát a fürtben. A minimum három csomópont, a maximális pedig tizenkét.

* **Fürtfelügyeleti jelszó** – Hozza létre a fürtfelügyelet jelszavát. Ez a jelszó a ```admin``` felhasználónévvel történik a fürt vezérlőpultjára való bejelentkezéshez, ahol figyelheti a fürtöt, és konfigurálhatja a fürt beállításait.

* **Avere vFXT-fürt neve** – Adjon a fürtnek egyedi nevet.

* **Méret** – Ez a szakasz a fürtcsomópontokhoz használt virtuálisgép-típust jeleníti meg. Bár csak egy ajánlott lehetőség van, a **Méret módosítása** hivatkozás megnyit egy táblázatot, amely az adott példánytípus részleteit tartalmazza, és egy árkalkulátora mutató hivatkozást.

* **Gyorsítótár mérete csomópontonként** – A fürtgyorsítótár a fürtcsomópontok között van elosztva, így az Avere vFXT-fürt teljes gyorsítótár-mérete megszorozza a csomópontok számát.

  Ajánlott konfiguráció: Csomópontonként 4 TB-ot használjon Standard_E32s_v3 csomóponthoz.

* **Virtuális hálózat** – Új virtuális hálózat definiálása a fürt elhelyezésére, vagy válasszon ki egy olyan meglévő hálózatot, amely megfelel az [Avere vFXT-rendszer megtervezése](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)című dokumentumban leírt előfeltételeknek.

  > [!NOTE]
  > Ha új virtuális hálózatot hoz létre, a fürtvezérlő nyilvános IP-címmel fog rendelkezni, így hozzáférhet az új magánhálózathoz. Ha meglévő virtuális hálózatot választ, a fürtvezérlő nyilvános IP-cím nélkül lesz konfigurálva.
  >
  > A fürtvezérlő nyilvánosan látható IP-címe könnyebb hozzáférést biztosít a virtuális géptéri fürthöz, de kis biztonsági kockázatot jelent.
  >* A fürtvezérlő nyilvános IP-címe lehetővé teszi, hogy ugróállomásként használja az Avere vFXT-fürthöz való csatlakozáshoz a magánalhálózaton kívülről.
  >* Ha nem rendelkezik nyilvános IP-címmel a vezérlőn, a fürt eléréséhez másik jump hostra, VPN-kapcsolatra vagy ExpressRoute-kapcsolatra van szükség. Használjon például egy olyan meglévő virtuális hálózatot, amelymár rendelkezik konfigurált VPN-kapcsolattal.
  >* Ha nyilvános IP-címmel rendelkező vezérlőt hoz létre, a vezérlő virtuális gépét egy hálózati biztonsági csoporttal kell védenie. Alapértelmezés szerint az Avere vFXT for Azure központi telepítése létrehoz egy hálózati biztonsági csoportot, amely korlátozza a bejövő hozzáférést csak a 22-es portra a nyilvános IP-címekkel rendelkező vezérlők számára. Tovább védheti a rendszert az IP-forráscímek tartományához való hozzáférés zárolásával , azaz csak a fürthozzáféréshez használni kívánt gépekkapcsolatait.

  Egy új virtuális hálózat is konfigurálva van egy tárolási szolgáltatás végpont az Azure Blob storage és a hálózati hozzáférés-vezérlés zárolva, hogy csak ip-címeket a fürt alhálózatból.

* **Alhálózat** – Válasszon alhálózatot, vagy hozzon létre egy újat.

* **Blob storage létrehozása és használata** – Válassza a **True** lehetőséget egy új Azure Blob-tároló létrehozásához, és konfigurálja az új Avere vFXT-fürt háttértárolójaként. Ez a beállítás egy új tárfiókot is létrehoz a fürt erőforráscsoportjában, és létrehoz egy Microsoft storage-szolgáltatás végpontot a fürt alhálózatán belül.
  
  Ha egy meglévő virtuális hálózatot biztosít, a fürt létrehozása előtt rendelkeznie kell egy tárolási szolgáltatás-végponttal. (További információkért olvassa el [az Avere vFXT rendszer megtervezése](avere-vfxt-deploy-plan.md).)

  Állítsa ezt a mezőt **hamisra,** ha nem szeretne új tárolót létrehozni. Ebben az esetben a fürt létrehozása után csatolnia és konfigurálnia kell a tárolót. Olvassa [el a Tároló konfigurálása](avere-vfxt-add-storage.md) című útmutatót.

* **(Új) Tárfiók** – Ha új Azure Blob-tárolót hoz létre, adja meg az új tárfiók nevét.

## <a name="validation-and-purchase"></a>Érvényesítés és beszerzés

A harmadik oldal összefoglalja a konfigurációt és ellenőrzi a paramétereket. Miután az ellenőrzés sikeres volt, ellenőrizze az összegzést, és kattintson az **OK** gombra.

> [!TIP]
> A fürt létrehozási beállításait az **OK** gomb melletti **Letöltés sablon és paraméterek** hivatkozásra kattintva mentheti. Ez az információ akkor lehet hasznos, ha később hasonló fürtöt kell létrehoznia – például egy cserefürt létrehozásához egy vész-helyreállítási forgatókönyvben. (További információért olvassa el a [vészhelyreállítási útmutatót.)](disaster-recovery.md)

![A telepítési sablon harmadik oldala - érvényesítés](media/avere-vfxt-deploy-3.png)

A negyedik oldal megadja a használati feltételeket, valamint az adatvédelmi és árképzési információkra mutató hivatkozásokat.

Adja meg a hiányzó kapcsolattartási adatokat, majd kattintson a **Létrehozás** gombra a feltételek elfogadásához és az Avere vFXT azure-fürt létrehozásához.

![A telepítési sablon negyedik oldala - feltételek, létrehozás gomb](media/avere-vfxt-deploy-4.png)

A fürt telepítése 15–20 percet vesz igénybe.

## <a name="gather-template-output"></a>Sablonkimenet összegyűjtése

Amikor az Avere vFXT sablon befejezi a fürt létrehozását, fontos információkat ad ki az új fürtről.

> [!TIP]
> Győződjön meg arról, hogy másolja a **felügyeleti IP-címet** a sablon kimenetéről. A fürt felügyeletéhez erre a címre van szükség.

Az információk megkereséséhez:

1. Nyissa meg a fürtvezérlő erőforráscsoportját.

1. A bal oldalon kattintson **a Központi telepítések**, majd **a microsoft-avere.vfxt-template elemre.**

   ![Erőforráscsoport portál lapja a bal oldalon kijelölt központi telepítések és a microsoft-avere.vfxt-sablon megjelenítése a Telepítés neve alatti táblában](media/avere-vfxt-outputs-deployments.png)

1. A bal oldalon kattintson **a Kimenetek gombra.** Másolja az egyes mezők értékeit.

   ![kimenetek lap, amelyen SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs és MGMT_IP értékek jelennek meg a címkéktől jobbra lévő mezőkben](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>További lépések

Most, hogy a fürt fut, és tudja a felügyeleti IP-címét, [csatlakozzon a fürt konfigurációs eszközéhez.](avere-vfxt-cluster-gui.md)

A konfigurációs felület segítségével testreszabhatja a fürtöt, beleértve az alábbi telepítési feladatokat is:

* [Támogatás engedélyezése](avere-vfxt-enable-support.md)
* [Tároló hozzáadása](avere-vfxt-add-storage.md) (ha szükséges)
