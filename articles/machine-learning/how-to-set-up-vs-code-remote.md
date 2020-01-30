---
title: 'Interaktív hibakeresés: VS Code & ML számítási példányok'
titleSuffix: Azure Machine Learning
description: Állítsa be a VS Code távirányítót a kód interaktív hibakereséséhez Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 27243f47df7da22ab4adda088bdf631c1030dd6c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845200"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Interaktív hibakeresés egy Azure Machine Learning számítási példányon és a VS Code Remote

Ebből a cikkből megtudhatja, hogyan állíthatja be a Visual Studio Code Remote-t egy Azure Machine Learning számítási példányon, hogy interaktív módon tudjon **hibakeresést végezni a** vs Code-ból. 

> [!NOTE]
> A számítási példányok csak az **USA északi középső**régiójában, az USA 2. keleti régiójában, **Észak-Európában** vagy **Egyesült Királyság déli régiója**található munkaterületek esetében érhetők el.

+ Az [Azure Machine learning számítási példány](concept-compute-instance.md) egy teljes körűen felügyelt felhőalapú munkaállomás az adatszakértők számára, és felügyeleti és vállalati készültségi képességeket biztosít a rendszergazdák számára. 


+ [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) A fejlesztés lehetővé teszi, hogy egy tárolót, távoli gépet vagy a Linux (WSL) Windows alrendszerét teljes funkcionalitású fejlesztői környezetként használja. 

## <a name="prerequisite"></a>Előfeltétel  

Windows-platformokon [telepítenie kell egy OpenSSH-kompatibilis SSH-ügyfelet](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , ha még nem létezik ilyen. 

> [!Note]
> A PuTTY nem támogatott a Windowsban, mivel az SSH-parancsnak az elérési úton kell lennie. 

## <a name="get-ip-and-ssh-port"></a>IP-cím és SSH-port beszerzése 

1. Nyissa meg a Azure Machine Learning studiót a https://ml.azure.com/ címen.

2. Válassza ki a [munkaterületet](concept-workspace.md).
1. Kattintson a **számítási példányok** fülre.
1. Az **alkalmazás URI** -oszlopában kattintson a távoli számításként használni kívánt számítási példány **SSH** -hivatkozására. 
1. A párbeszédpanelen jegyezze fel az IP-címet és az SSH-portot. 
1. Mentse a titkos kulcsot a ~/.ssh/könyvtárba a helyi számítógépen; például nyisson meg egy szerkesztőt egy új fájlhoz, és illessze be a kulcsot a következőbe: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A titkos kulcs valahogy így fog kinézni:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Módosítsa a fájl engedélyeit, és győződjön meg arról, hogy csak a fájlt tudja olvasni.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Példány hozzáadása gazdagépként 

Nyissa meg `~/.ssh/config` (Linux) vagy `C:\Users<username>.ssh\config` (Windows) fájlt egy szerkesztőben, és adjon hozzá egy ehhez hasonló új bejegyzést:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

További részletek a mezőkről: 

|Mező|Leírás|
|----|---------|
|Gazdagép|Bármilyen gyorsírást használhat a számítási példányhoz |
|HostName|Ez a számítási példány IP-címe. |
|Port|Ez a port a fenti SSH-párbeszédablakban látható. |
|Felhasználó|Ezt `azureuser` kell |
|IdentityFile|Arra a fájlra mutasson, ahová a titkos kulcsot mentette |

Mostantól a fentiekben használt gyorsírással SSH-t kell tudnia használni a számítási példányhoz, `ssh azmlci1`. 

## <a name="connect-vs-code-to-the-instance"></a>A VS Code összekötése a példánnyal 

1. [Telepítse a Visual Studio Code](https://code.visualstudio.com/)-ot.

1. [Telepítse a távoli SSH-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Az SSH-konfigurációk megjelenítéséhez kattintson a bal oldali távoli SSH ikonra.

1. Kattintson a jobb gombbal az imént létrehozott SSH-gazda konfigurációra.

1. Válassza a **Kapcsolódás a gazdagéphez lehetőséget az aktuális ablakban**. 

Innentől kezdve teljes mértékben dolgozik a számítási példányon, és mostantól szerkesztheti, hibakeresést végezhet, használhatja a git-t, a bővítmények használatát stb. – akárcsak a helyi Visual Studio Code-ban. 

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a Visual Studio Code Remote-t, a Visual Studio Code-ból távoli számításként használhat számítási példányt a kód interaktív hibakereséséhez. 

[Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.