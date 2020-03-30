---
title: 'Interaktív hibakeresés: VS Code & ML számítási példányok'
titleSuffix: Azure Machine Learning
description: Állítsa be a VS Code Remote-ot, hogy interaktívan debugolja a kódot az Azure Machine Learning segítségével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169754"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Interaktív hibakeresés egy Azure Machine Learning számítási példányon vs code remote-tal

Ebben a cikkben megtudhatja, hogyan állíthatja be a Visual Studio Code Remote egy Azure Machine Learning számítási példányt, így **interaktívan hibakeresés a kódot** vs kód. 

+ Az [Azure Machine Learning Compute Instance](concept-compute-instance.md) egy teljes körűen felügyelt felhőalapú munkaállomás adatszakértők számára, és felügyeleti és vállalati készenléti képességeket biztosít a rendszergazdák számára. 


+ [Visual Studio kód távirányító](https://code.visualstudio.com/docs/remote/remote-overview) A fejlesztés lehetővé teszi, hogy teljes értékű fejlesztői környezetként használjon tárolót, távoli gépet vagy a Windows Linux-alrendszert (WSL). 

## <a name="prerequisite"></a>Előfeltétel  

Windows platformokon telepítenie kell [egy OpenSSH-kompatibilis SSH-ügyfelet,](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) ha még nincs jelen. 

> [!Note]
> A PuTTY nem támogatott Windows rendszeren, mivel az ssh parancsnak az elérési úton kell lennie. 

## <a name="get-ip-and-ssh-port"></a>IP- és SSH-port beszerezése 

1. Nyissa meg az Azure https://ml.azure.com/Machine Learning stúdióját a.

2. Válassza ki a [munkaterületet](concept-workspace.md).
1. Kattintson a **Számítási példányok** fülre.
1. Az **alkalmazás** URI-oszlopában kattintson a távoli számítási ként használni kívánt számítási példány **SSH-hivatkozására.** 
1. A párbeszédpanelen vegye figyelembe az IP-cím és az SSH-portot. 
1. Mentse a személyes kulcsot a ~/.ssh/ könyvtárba a helyi számítógépen; például nyisson meg egy szerkesztőt egy új fájlhoz, és illessze be a kulcsot a következő be: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A személyes kulcs valahogy így fog kinézni:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Módosítsa a fájlra vonatkozó engedélyeket, és győződjön meg arról, hogy csak ön tudja elolvasni a fájlt.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Példány hozzáadása állomásként 

Nyissa meg `~/.ssh/config` a fájlt `C:\Users<username>.ssh\config` (Linux) vagy (Windows) egy szerkesztőben, és adjon hozzá egy ehhez hasonló új bejegyzést:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Itt néhány részletet a mezők: 

|Mező|Leírás|
|----|---------|
|Gazdagép|Használja bármilyen gyorsírást a számítási példányhoz |
|HostName|Ez a számítási példány IP-címe |
|Port|Ez a port látható a fenti SSH párbeszédpanelen |
|Felhasználó|Ezt meg kell `azureuser` |
|IdentityFile|Arra a fájlra kell mutatnia, ahová a személyes kulcsot mentette |

Most már képesnek kell lennie arra, hogy ssh a számítási `ssh azmlci1`példány segítségével a gyorsírás a fent használt, . 

## <a name="connect-vs-code-to-the-instance"></a>A VS-kód csatlakoztatása a példányhoz 

1. [Telepítse a Visual Studio-kódot](https://code.visualstudio.com/).

1. [Telepítse a távoli SSH bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Az SSH-konfigurációk megjelenítéséhez kattintson a bal oldali Remote-SSH ikonra.

1. Kattintson a jobb gombbal az imént létrehozott SSH állomáskonfigurációra.

1. Az Aktuális ablakban válassza **az Állomáshoz való csatlakozás lehetőséget.** 

Innen kezdve teljes mértékben a számítási példányon dolgozik, és most már szerkesztheti, debugolhatja, használhatja a git-et, használhatja a bővítményeket stb. 

## <a name="next-steps"></a>További lépések

Most, hogy beállította a Visual Studio Code Remote-ot, a Visual Studio-kód távoli számítási példányaként interaktív hibakeresést biztosíthat a kód ban. 

[Oktatóanyag: Az első gépi tanulási modell betanítása](tutorial-1st-experiment-sdk-train.md) bemutatja, hogyan használhat számítási példányt egy integrált notebookkal.