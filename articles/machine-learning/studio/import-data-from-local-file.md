---
title: Adatokat importálhat egy fájlt Azure Machine Learning Studio |} Microsoft Docs
description: 'Útmutató: Azure Machine Learning Studio a merevlemez-meghajtóról adatok képzési fájl feltöltéséhez. Ez létrehoz egy olyan adatkészlet modult a munkaterületen.'
keywords: adatok, adatformátum, adattípusok, adatforrások, betanítási adatok importálása
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 0365492b2814d686dd0bfa099e94717137b51725
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834770"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Tanítási adatokat importálhat egy fájlt a merevlemezen a Machine Learning Studióhoz
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Ismerje meg, hogyan tölthet fel a merevlemez-meghajtóról kívánja használni, mint az Azure Machine Learning Studióban betanítási adatok adatfájlt. Importálja az adatfájlban, rendelkezik egy adatkészlet modul használatra kész a munkaterület.

## <a name="steps-to-import-data-from-a-local-file"></a>Adatokat importálhat egy helyi fájl lépései
Adatokat importálhat egy helyi merevlemezen, tegye a következőket:

1. Kattintson a **+ új** a Machine Learning Studio ablakának alján.
2. Válassza ki **DATASET** és **helyi FÁJLBÓL**.
3. Az a **töltse fel az új adatkészlet** párbeszédpanelen keresse meg a feltölteni kívánt fájl
4. Adjon meg egy nevet, és azonosíthatja a adattípus ismertetésének. Olyan leírást ajánlott - lehetővé teszi bármely jellemzőinek, amelyet a későbbiekben az adatok használatakor vegye figyelembe az adatok rögzítéséhez.
5. A jelölőnégyzet **egyik meglévő adatkészletét új verziója** lehetővé teszi egy meglévő adatkészlet frissítése az új adatokat. Ezt a jelölőnégyzetet, és írja be egy meglévő adatkészlet nevét.

![Töltse fel az új adatkészlet](./media/import-data-from-local-file/upload-dataset.png)

Feltöltés közben megjelenik egy üzenet, hogy a fájl feltöltése van folyamatban. Töltse fel idő az adatok méretétől és a szolgáltatásnak a kapcsolat sebességétől függ. Ha tudja, hogy a fájl hosszú időt vesz igénybe, megteheti a Machine Learning Studio belül egyebek, várakozás közben. Azonban a böngésző bezárásával hatására az adatok feltöltése sikertelen.

## <a name="dataset-module-is-ready-for-use"></a>A DataSet modul az használatra kész
Az adatok a feltöltést követően egy adatkészlet modulban tárolja, és bármilyen kísérlet a munkaterület érhető el.

A kísérlet szerkesztésekor a létrehozott adathalmazok található a **saját adatkészletek** listában az a **mentett adatkészletek** a modulpalettán lista. Húzza, és dobja el az adatkészletet a kísérlet vászonra, ha meg szeretné használni a dataset további elemzés és a gépi tanulás.
