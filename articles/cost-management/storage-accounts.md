---
title: Storage-fiókok konfigurálása az Azure költség Management szolgáltatáshoz |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálja az Azure storage-fiókok és AWS tárolási gyűjtők Azure költség Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: e37604e5cd36cfed016ef596060459011ec32d35
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297835"
---
# <a name="configure-storage-accounts-for-cost-management"></a>Storage-fiókok konfigurálása a költség Management szolgáltatáshoz

<!--- intent: As a Cost Management user, I want to configure Cost Management to use my cloud service provider storage account to store my reports. -->

A Cloudyn portál, az Azure storage vagy AWS tárolási gyűjtők költség jelentések mentheti. A jelentés mentése a Cloudyn portálra az ingyenesen elérhető. Azonban a jelentés mentése a felhőbeli szolgáltatás szolgáltatója tárolási nem kötelező, és költséget további áll. Ez a cikk segít az Azure storage-fiókok és az Amazon Web Services (AWS) tárolási gyűjtők tárolja a jelentések konfigurálása.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy Azure storage-fiókot vagy egy Amazon tárolási időszakot.

Ha nem rendelkezik egy Azure storage-fiók, létre kell hoznia egyet. Egy Azure storage-fiók létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Ha még nem rendelkezik az AWS egyszerű tárolási szolgáltatás (S3) gyűjtőjét, kell létrehoznia egyet. Egy S3 gyűjtő létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy gyűjtőben](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Az Azure storage-fiókok konfigurálása

Konfigurálása, hogy az Azure storage költség felügyeleti által használható egy egyszerű. Gyűjtse össze a tárfiók adatait, és másolja a fájlokat a Cloudyn portálon.

1. Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.
2. Kattintson a **minden szolgáltatás**, jelölje be **tárfiókok**, keresse meg a storage-fiók, amelyet szeretne használni, és válassza ki a fiókot.
3. A tárolási fiók lapon alatt **beállítások**, kattintson a **hívóbetűk**.
4. Másolás a **tárfióknév** és **kapcsolati karakterlánc** key1 alatt.  
![Az Azure storage elérési kulcsok](./media/storage-accounts/azure-storage-access-keys.png)  
5. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra https://azure.cloudyn.com , jelentkezzen be.
6. Válassza a fogaskerékre szimbólum **jelentések tárolókezelési**.
7. Kattintson a **új hozzáadása +** , és győződjön meg arról, hogy a Microsoft Azure van kiválasztva. Illessze be a az Azure storage-fiók nevére, a a **neve** területen. Beillesztés a **kapcsolati karakterlánc** a megfelelő területen. Adja meg a tároló nevét, és kattintson a **mentése**.  
![Cloudyn tárolási kapacitással, az Azure-bA](./media/storage-accounts/azure-cloudyn-storage.png)

  A storage-fiókok listáján megjelenik az új Azure jelentés tárolási bejegyzés.  
    ![Új Azure jelentés tárhely a listában](./media/storage-accounts/azure-storage-entry.png)


Az Azure storage most menthet jelentést. A bármely jelentés **műveletek** majd **ütemezni a jelentést**. A jelentés nevet majd adja hozzá a saját URL-címet, vagy pedig az automatikusan létrehozott URL-CÍMÉT. Válassza ki **tárolási mentése** , és válassza ki a tárfiók. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a fürt megosztott kötetei szolgáltatás vagy a JSON formátumú fájlokat, és mentse a jelentést.

## <a name="configure-an-aws-storage-bucket"></a>Az AWS tárolási gyűjtő konfigurálása

A Cloudyn meglévő AWS hitelesítő adatokat használja az: felhasználó vagy a szerepkör, a jelentések menthetők a gyűjtő számára. A hozzáférés teszteléséhez Cloudyn próbál egy kisméretű szöveges fájl mentéséhez a gyűjtő a fájlnévvel _jelölőnégyzet-gyűjtő-permission.txt_.

Megadja a Cloudyn szerepkör vagy felhasználó PutObject engedéllyel rendelkező a gyűjtő számára. Ezután egy meglévő időszakot használja, vagy hozzon létre egy új jelentés mentéséhez. Végezetül annak eldöntése, hogyan kezelheti a tárolási osztály, életciklus szabályokat állíthat be, vagy távolítsa el a felesleges fájlokat.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Az AWS felhasználó vagy szerepkör engedélyek hozzárendelése

Ha egy új házirendet hoz létre, meg kell adnia a pontos jelentés mentése a S3 gyűjtő szükséges engedélyekkel.

1. Jelentkezzen be az AWS konzol, majd válassza ki **szolgáltatások**.
2. Válassza ki **IAM** szolgáltatások közül.
3. Válassza ki **házirendek** a konzolon, és kattintson a bal oldalon **házirend létrehozása**.
4. Kattintson a **JSON** fülre.
5. A következő házirendje lehetővé teszi egy S3 gyűjtő mentse a jelentést. Másolja és illessze be a következő házirend példát a **JSON** fülre. Cserélje le &lt;bucketname&gt; a gyűjtő névvel.

  ```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
}
```

6. Kattintson a **Irányelveiből**.  
    ![Tekintse át a házirend](./media/storage-accounts/aws-policy.png)  
7. A felülvizsgálati házirend lapon írja be a házirend nevét. Például _CloudynSaveReport2S3_.
8. Kattintson a **házirend létrehozása**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>A szabályzat csatolása Cloudyn szerepkör vagy felhasználó fiókja

Csatolni az új házirend, az AWS konzol megnyitásához, és a Cloudyn szerepkör vagy felhasználó szerkesztése.

1. Jelentkezzen be az AWS konzol, majd válassza ki **szolgáltatások**, majd jelölje be **IAM** szolgáltatások közül.
2. Válassza ki vagy **szerepkörök** vagy **felhasználók** a konzol bal oldalán.

**A szerepkörök:**

  1. Kattintson a Cloudyn szerepkör nevét.
  2. Az a **engedélyek** lapra, majd **csatolása házirend**.
  3. Keresse meg a létrehozott házirendet és válassza ki azt, majd kattintson a **csatolása házirend**.
    ![AWS - házirend egy szerepkör csatolása](./media/storage-accounts/aws-attach-policy-role.png)

**A felhasználók számára:**

1. Válassza ki a Cloudyn felhasználót.
2. Az a **engedélyek** lapra, majd **engedélyek hozzáadása**.
3. Az a **Grant engedéllyel** szakaszban jelölje be **közvetlenül csatolni a meglévő házirendek**.
4. Keresse meg a létrehozott házirendet és válassza ki azt, majd kattintson a **tovább: felülvizsgálati**.
5. Kattintson a Hozzáadás engedélyekkel neve szerepkörlap, **engedélyek hozzáadása**.  
    ![AWS - egy felhasználóra vonatkozó szabályzattal csatolása](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Választható lehetőség: A gyűjtő házirend engedély beállítása

Jelentéseket hozhat létre, a gyűjtő házirend segítségével S3 gyűjtő jogosultsága állíthat be. A klasszikus S3 nézetben:

1. Létrehozhat vagy kiválaszthat egy meglévő időszakot.
2. Válassza ki a **engedélyek** fülre, majd **gyűjtőjét házirend**.
3. Másolja és illessze be a következő házirend-mintát. Cserélje le &lt;gyűjtőjét\_neve&gt; és &lt;Cloudyn\_elv&gt; rendelkező, a gyűjtő információ. Cserélje le a szerepkör vagy a felhasználó Cloudyn által használt információ.

  ```
{
  "Id": "Policy1485775646248",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
  ]
}
```

4. A gyűjtő csoportházirend-szerkesztőben kattintson **mentése**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Cloudyn AWS jelentés tároló hozzáadása

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra https://azure.cloudyn.com , jelentkezzen be.
2. Válassza a fogaskerékre szimbólum **jelentések tárolókezelési**.
3. Kattintson a **új hozzáadása +** , és győződjön meg arról, hogy AWS van kiválasztva.
4. Válasszon egy fiókot és a tárolási időszakot. Az AWS tárolási gyűjtő neve automatikusan kitöltött-a.  
    ![A jelentés tároló AWS gyűjtő](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kattintson a **mentése** majd **Ok**.

    A storage-fiókok listáján megjelenik az új AWS jelentés tárolási bejegyzés.  
    ![Új AWS jelentés tárhely a listában](./media/storage-accounts/aws-storage-entry.png)


Az Azure storage most menthet jelentést. A bármely jelentés **műveletek** majd **ütemezni a jelentést**. A jelentés nevet majd adja hozzá a saját URL-címet, vagy pedig az automatikusan létrehozott URL-CÍMÉT. Válassza ki **tárolási mentése** , és válassza ki a tárfiók. Adja meg, amely lekérdezi a jelentés fájlnév fűzött előtagot. Válassza ki a fürt megosztott kötetei szolgáltatás vagy a JSON formátumú fájlokat, és mentse a jelentést.

## <a name="next-steps"></a>További lépések

- Felülvizsgálati [ismertetése költség jelentések](understanding-cost-reports.md) alapszintű struktúrát és költség jelentések funkcióit.
