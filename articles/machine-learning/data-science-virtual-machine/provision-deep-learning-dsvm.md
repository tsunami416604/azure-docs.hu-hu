---
title: A mély képzési adatok tudományos virtuális gépet az Azure telepítéséhez |} Microsoft Docs
description: Konfigurálja és részletes képzési adatok tudományos virtuális gép létrehozása Azure elemzéséhez és a gépi tanulás.
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
ms.openlocfilehash: 2172acfd61188c09693b9c7dacf37ff7a638d7f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169941"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>A virtuális gépet az Azure tanulási mély kiépítése 

A részletes tanulási virtuális gép (DLVM) a népszerű kifejezetten konfigurált variant [adatok tudományos virtuális gép](http://aka.ms/dsvm) gyorsan a mély tanulási modellek betanítása példányának (DSVM) használatával könnyebben GPU-alapú virtuális gép használja. Az objektum támogatott Windows 2016 vagy az Ubuntu DSVM. A DLVM közösen használja az ugyanazon alapvető VM-lemezképekkel, és ezért a gazdag eszközkészlet DSVM érhető el. 

A DLVM több eszközt tartalmaz AI, beleértve a GPU kiadásait mély tanulási népszerű keretrendszerekre pl. Microsoft kognitív eszközkészlet TensorFlow, Keras, Caffe2, Chainer; eszközök beszerzése és előre folyamatok kép, szöveges adatok, adatok tudományos modellezési és fejlesztési tevékenységek, például Microsoft R Server Developer Edition, Anaconda Python, Python a Jupyter notebookokból és R, Python és R, SQL IDEs eszközök adatbázisok, és számos egyéb adatok tudományos és ML eszközök. 

## <a name="create-your-deep-learning-virtual-machine"></a>A Virtual Machine Learning mély létrehozása
A részletes tanulási-virtuális gép példány létrehozásához lépései a következők: 

1. Keresse meg a virtuális gépet, a listaelem [Azure-portálon](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Válassza ki a **létrehozása** panel alján, a varázsló veendő.![ Hozzon létre dlvm](./media/dlvm-provision-wizard.PNG)
3. A varázsló a DLVM létrehozásához használt szükséges **bemenetek** az egyes a **négy lépésben** a jobb oldali ábra számba. Az alábbiakban az egyes lépéseket konfigurálásához szükséges adatokat:
   
   1. **Alapvető beállítások**
      
      1. **Név**: az adatok tudományos kiszolgáló létrehozásakor nevét.
      2. **Válassza ki az operációs rendszer típusát a mély tanulási virtuális gép**: válassza a Windows vagy Linux (a Windows 2016 és Ubuntu Linux alap DSVM)
      2. **Felhasználónév**: rendszergazdai fiók bejelentkezési azonosító.
      3. **Jelszó**: rendszergazdai fiók jelszavát.
      4. **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van.
      5. **Erőforráscsoport**: hozhat létre egy új, vagy használjon egy **üres** meglévő Azure erőforráscsoport, az előfizetésben.
      6. **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb. 
      
> [!NOTE]
> A DLVM minden NC és ND adatsorozat GPU Virtuálisgép-példányok támogatja. A DLVM létesítésekor választania kell a helyek Feldolgozóegységekkel rendelkező Azure-ban. Ellenőrizze a [Azure termékek régió lap](https://azure.microsoft.com/regions/services/) lapon az elérhető helyeket, és keresse meg **NC-sorozat**, **NCv2-sorozat**, **NCv3-sorozat** , vagy **ND-sorozat** alatt **számítási**. 

   2. **Beállítások**: Válasszon egy hálózati vezérlő által adatsorozat (NC, NCv2, NCv3) vagy ND adatsorozat GPU virtuálisgép-méretek, amely megfelel a funkcionális és költség megkötések. Hozzon létre egy tárfiókot a virtuális gép számára.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   3. **Összefoglalás**: Győződjön meg arról, hogy az összes megadott adatok helyesek.
   5. **Vásároljon**: kattintson a **megvásárlása** kiépítési elindításához. Hivatkozás a tranzakció feltételeit valósul meg. A virtuális gép nem rendelkezik a kiválasztott kiszolgáló méretéhez számítási túl további díjakat a **mérete** lépés. 

> [!NOTE]
> A kiépítése körülbelül 10-20 percet kell végrehajtani. A kiépítési állapotát az Azure portálon jelenik meg.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Hogyan érhetők el a részletes tanulási virtuális gép

### <a name="windows-edition"></a>Windows Edition
A virtuális gép létrehozása után azokat a rendszergazdai fiók hitelesítő adatait az előző konfigurált használatával is a távoli asztal **alapjai** szakasz. 

### <a name="linux-edition"></a>Linux Edition

A virtuális gép létrehozása után is bejelentkezik az ssh protokoll használatával. A létrehozott fiók hitelesítő adatait használja a **alapjai** szakasz 3. lépés a szöveg shell felületén. Windows-ügyfél, egy SSH-ügyfél eszköz, például letöltheti [Putty](http://www.putty.org). Grafikus asztali (X Windows rendszer) tetszés szerint használhatja a Putty továbbítási X11, de X2Go ügyfél telepítése.

> [!NOTE]
> A X2Go ügyfél jobban teljesített, mint a tesztelés során továbbítás X11. Egy asztali grafikus felület a X2Go ügyfél használatát javasoljuk.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Telepítése és konfigurálása X2Go ügyfél
A Linux DLVM már kiépített X2Go kiszolgálóval és készen áll kapcsolatok fogadására. A Linux virtuális gép grafikus asztali való kapcsolódáshoz az alábbi eljárással az ügyfélen:

1. Töltse le és telepítse a saját ügyfélplatformjára X2Go ügyfél [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Futtassa a X2Go ügyfél, és válassza a **új munkamenet**. Több lap egy konfigurációs ablak nyílik meg. Adja meg a következő konfigurációs paramétereket:
   * **Munkamenet lapon**:
     * **Állomás**: az állomásnév vagy IP-címét a Linux adatok tudományos virtuális Gépet.
     * **Bejelentkezési**: felhasználónév, a Linux virtuális Gépre.
     * **SSH-Port**: hagyja a mezőt, 22, az alapértelmezett érték.
     * **Munkamenet típusa**: módosítsa a beállítást **XFCE**. A Linux DSVM jelenleg csak XFCE desktop támogatja.
   * **Az adathordozó lapon**: kikapcsolhatja a hang támogatása és az ügyfél nyomtatási, ha nincs szüksége is használhatja őket.
   * **Megosztott mappák**: Ha az ügyfél gépek, a Linux virtuális gép csatlakoztatott könyvtárak, az ügyfél gép címtárakat, a virtuális gép ezen a lapon megosztani kívánt fel.

Bejelentkezés után a virtuális gép SSH-ügyfél vagy a XFCE grafikus asztali a X2Go ügyfélen keresztül, készen áll az eszközök, amelyek telepítése és konfigurálása történik meg a virtuális Gépen elindítására. A XFCE látható alkalmazások parancsikonjai és asztali ikonok esetében számos eszközt.

Miután a virtuális gép létrehozása és üzembe helyezve, készen áll indíthatja azon eszközöket, amelyek telepítése és konfigurálása történik meg. Start menü csempék és számos eszközt az asztali ikonok vannak. 
