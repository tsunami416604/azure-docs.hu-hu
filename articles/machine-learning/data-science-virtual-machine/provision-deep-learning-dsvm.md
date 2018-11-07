---
title: Deep Learning-Azure-beli adatelemző virtuális gép kiépítése |} A Microsoft Docs
description: Konfigurálja és a egy Deep Learning-adatelemzési virtuális gép létrehozása az Azure-ban Analytics és a gépi tanulás.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9d64ad70ea49f7fbffd8bd6a5a77177fe490b832
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229662"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Deep Learning-Azure-beli virtuális gép kiépítése 

A Deep Learning virtuális gép (DLVM) egy speciálisan konfigurált változata a népszerű [adatelemző virtuális gép](https://aka.ms/dsvm) , hogy könnyebben használható GPU-alapú virtuális gép (DSVM)-példányok gyorsan a deep learning-modellek betanításához. Az alap, a Windows 2016 vagy az Ubuntu DSVM támogatott. A DLVM megosztja az ugyanazon alapvető Virtuálisgép-rendszerképek és ezért a gazdag eszközkészletet, amely elérhető a dsvm-hez. 

A DLVM számos olyan eszközt tartalmaz, a mesterséges Intelligencia, ideértve például a Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; számos népszerű deep learning keretrendszerek GPU kiadásai eszközök beszerzésére és előfeldolgozási kép, szöveges adatok, eszközök adatelemzési modellezéshez és fejlesztési tevékenységekhez például a Microsoft R Server Developer Edition, Anaconda Python, a Jupyter notebooks, a Python és az R, Python és az SQL, R ide-ket adatbázisokat és sok más adatelemzési és gépi Tanulási eszközökkel. 

## <a name="create-your-deep-learning-virtual-machine"></a>A Mélytanulási virtuális gép létrehozása
Hozzon létre egy példányt, a Deep Learning virtuális gép a lépései a következők: 

1. Keresse meg a virtuális gépet, az ajánlati [az Azure portal](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Válassza ki a **létrehozás** gombra az alsó kell figyelembe venni a varázslót.![ Hozzon létre dlvm](./media/dlvm-provision-wizard.PNG)
3. A varázsló a DLVM létrehozásához használt szükséges **bemenetek** minden a **négy lépést** jobb oldalán Ez az ábra számba. Az alábbiakban a bemeneti adatok konfigurálása az egyes lépéseket:
   
   1. **Alapvető beállítások**
      
      1. **Név**: a data science kiszolgáló hoz létre nevet.
      2. **Válassza ki a Deep Learning virtuális gép operációs rendszerének típusa**: válassza a Windows vagy Linux (a Windows 2016 és Ubuntu Linux alap dsvm-hez)
      2. **Felhasználónév**: rendszergazdai fiók bejelentkezési azonosítója.
      3. **Jelszó**: rendszergazdai fiók jelszava.
      4. **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki az egyik, amelyen a gép létrehozása és a számlázás.
      5. **Erőforráscsoport**: létrehozhat egy újat, vagy használjon egy **üres** meglévő Azure-erőforráscsoport az előfizetésben.
      6. **Hely**: válassza a leginkább megfelelő adatközpontot. Általában az adatközpont, amely tartalmazza a legtöbb az adatokat, vagy a leggyorsabb hálózati hozzáféréshez a fizikai helyéhez legközelebbi. 
      
> [!NOTE]
> A DLVM támogatja az összes hálózati vezérlő és ND sorozatú GPU VM-példányokon. A DLVM kiépítésekor választania kell a helyek gpu-kkal rendelkező Azure-ban. Ellenőrizze a [régió lap által az Azure-termékekkel](https://azure.microsoft.com/regions/services/) lapon az elérhető helyeket, és keressen **NC sorozat**, **NCv2 sorozat**, **az NCv3 sorozatú** , vagy **ND sorozat** alatt **számítási**. 

   2. **Beállítások**: válassza ki az egyik az NC sorozat (NC, az NCv2, az NCv3) vagy az ND sorozat virtuálisgép-méretek GPU, amely megfelel a funkcionális és a költségek megkötések. Hozzon létre egy tárfiókot a virtuális géphez.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Összefoglalás**: Győződjön meg arról, hogy minden, a megadott adatok helyesek.
   5. **Vásároljon**: kattintson a **vásárlása** a kiépítésének megkezdéséhez. A tranzakció feltételeiben szerepel egy hivatkozás. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretét a számítási túl további díjakat a **mérete** . lépés. 

> [!NOTE]
> A kiépítés körülbelül 10 – 20 percet vesz igénybe. A kiépítési állapota jelenik meg az Azure Portalon.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>A Deep Learning virtuális gép elérése

### <a name="windows-edition"></a>Windows Edition
A virtuális gép létrehozása után azokat a rendszergazdai fiók hitelesítő adatait, amely az előző konfigurálta a távoli asztal is **alapjai** szakaszban. 

### <a name="linux-edition"></a>Linux Edition

Ha a virtuális gép létrejött, bejelentkezhet hozzá SSH használatával. A fiók hitelesítő adataival, amelyet a **alapjai** szakasz 3. lépésben a szöveg shell felületén. Windows-ügyfél letöltheti egy SSH-ügyfél eszköz, például [Putty](http://www.putty.org). Ha inkább egy grafikus desktop (X Windows rendszer), használhatja a Putty-továbbítás X11 vagy X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint a tesztelés továbbítás X11. Azt javasoljuk, hogy az asztali grafikus felület X2Go ügyfél használatával.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Telepítésével és konfigurálásával X2Go ügyfél
A Linux DLVM már kiépített X2Go kiszolgálóval, és készen áll kapcsolatok fogadására. Ha csatlakozni szeretne a Linuxos virtuális gép grafikus asztali, az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablaka nyílik. Adja meg az alábbi konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Gazdagép**: A gazdagép neve vagy a Linux rendszerű adatelemző virtuális gép IP-címét.
     * **Bejelentkezési**: felhasználónév, a Linux rendszerű virtuális gépen.
     * **SSH-Port**: meghagyhatja az alapértelmezett érték 22.
     * **Munkamenet típusa**: módosítsa az értéket **XFCE**. A Linux-DSVM jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: kikapcsolhatja a hang támogatása és az ügyfél nyomtatási, ha nem szeretné használni őket.
   * **A megosztott mappák**: Ha az ügyfél gépek csatlakoztatása Linux rendszerű virtuális gépen szeretné címtárak, adja hozzá az ügyfél gép könyvtárakat, hogy meg szeretné osztani az ezen a lapon a virtuális géppel.

Bejelentkezés után a virtuális géphez SSH-ügyfél vagy a grafikus asztali XFCE X2Go ügyfélszámítógépen keresztül, készen áll az eszközöket, amelyek telepítése és konfigurálása történik meg a virtuális gép használatának megkezdéséhez. A XFCE látható alkalmazások parancsikonjai és asztali ikonok a számos olyan eszközzel.

A virtuális gép létrehozása és üzembe helyezett, után készen áll az eszközöket, amelyek telepítése és konfigurálása történik rá használatának megkezdéséhez. Start menü csempék és számos olyan eszközzel asztali ikonok vannak. 
