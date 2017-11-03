---
title: "Unity összegző golyó oktatóanyag"
description: "Lépések végrehajtásával hozza létre a Unity klasszikus Roll egy golyó játék, amely előzetesen szükséges összes Mobile Engagement Unity-oktatóanyag"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 6392d1f780b1bc2348fee5947550b05e86ea4de2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a id="unity-roll-a-ball"></a>Hozzon létre Unity Roll golyó játék
Ez az oktatóanyag végigvezeti egy kis mértékben módosított fő lépései [Unity visszaállítani egy golyó oktatóanyag](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial). Ez a minta játék egy gömb "player" objektumon, amelynek az alkalmazás felhasználói által szabályozott áll, és a játék célja, hogy a "collect" memóriából kiüríthető objektumok által a memóriából kiüríthető objektumok player objektum ütközés. Ez azt feltételezi, hogy a Unity-szerkesztő környezet alapszintű ismeretét. Ha probléma merül fel, majd tekintse át a teljes útmutató. 

### <a name="setting-up-the-game"></a>A game beállítása
A rendszer az alábbi lépéseket a [Unity oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Nyissa meg **Unity-szerkesztő** kattintson **új**. 
   
    ![][51] 
2. Adjon meg egy **projektnevet** & **hely**, jelölje be **3D** kattintson **Create project**.
   
    ![][52]
3. Mentse a részeként az új projekt nevét az imént létrehozott alapértelmezett helyszín **MiniGame** belül egy új  **\_színfalak** mappát **eszközök** mappába:
   
    ![][53]
4. Hozzon létre egy **3D objektum -> Vezérlősík** a játszik mezőben, és nevezze át ezt az vezérlősík objektumot **Ground**
   
    ![][1]
5. Az átalakítási összetevő alaphelyzetbe a **Ground** objektumot, hogy a forráson van. 
   
    ![][3]
6. Törölje a jelet **rácsvonalak megjelenítése** a **Gizmos menü** a a **Ground** objektum.
   
    ![][4]
7. Frissítés a **méretezési** az összetevőt a **Ground** objektumtípus [X = 2, Y = 1, Z = 2]. 
   
    ![][5]
8. Adjon hozzá egy új **3D objektum -> kapcsolataikat** a projekt és az eddigi objektum, nevezze át **Player**. 
   
    ![][6]
9. Válassza ki a **Player** objektumra, és kattintson a **átalakítás visszaállítása** hasonló, és a Vezérlősík objektum. 
10. Frissítés **átalakító -> pozíció -> Y koordinátáját** 0,5 Player Y az összetevőt.  
    
    ![][7]
11. Hozzon létre egy új nevű **anyagok** a projektben, ahol az anyag a Windows Media player színes létre fogunk hozni. 
12. Hozzon létre egy új **anyag** nevű **háttér** ebben a mappában. 
    
    ![][8]
13. Az anyag színének frissítése frissítésével a **Albedo** tulajdonsága azt. Kiválaszthatja a [0,32,64] RGB-értékeket. 
    
    ![][9]
14. Húzza a leképezni kívánt jelenetben nézet színnel anyagot a **Ground** objektum. 
    
    ![][10]
15. Végül frissítse a **átalakító -> Elforgatás -> Y** 60 jobb érthetőség kedvéért bizonyos irányt világos objektumon. 
    
    ![][12]

### <a name="moving-the-player"></a>A Windows Media player áthelyezése
A rendszer az alábbi lépéseket a [Unity oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Adja hozzá a **RigidBody** összetevőt, hogy a **Player** objektum. 
   
    ![][13]
2. Hozzon létre egy új nevű **parancsfájlok** a projektben. 
3. Kattintson a **összetevőt -> Új parancsfájl -> C# parancsfájl**. Nevezze el **PlayerController**, és kattintson a **létrehozása és hozzáadása**. Ezzel a hoz létre, és egy parancsfájl csatolása a Player objektumhoz.  
   
    ![][14]
4. Ezt a parancsfájlt a helyezze át a **parancsfájlok** a projekt mappájára. 
5. Nyissa meg szerkesztésre a kedvenc parancsprogram-szerkesztő, a script kód frissítése a következő kódra, és mentse azokat. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. Vegye figyelembe, hogy a fenti parancsfájl használ egy **sebesség** tulajdonság. A Unity-szerkesztőben frissítse a sebesség tulajdonság 10.  
   
    ![][15]
7. Találati **lejátszása** a Unity-szerkesztőben. Most kell vezérelhetik a billentyűzettel golyó, és azt kell elforgatás és navigálás. 

### <a name="moving-the-camera"></a>A kamera áthelyezése
A rendszer az alábbi lépéseket a [Unity-oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) és lelassul a **fő kamera** számára a **Player** objektum. 

1. Frissítés a **Transform.Position** X kell = 0, Y 10.5, Z-= 10 =.  
2. Frissítés a **Transform.Rotation** X kell = 45, I = 0, Z = 0.  
   
    ![][16]
3. Nevű új parancsfájl hozzáadása **CameraController** számára a **MainCamera** , és a parancsfájlok mappa alá helyezi. 
   
    ![][17]
4. Nyissa meg szerkesztésre a parancsfájlt, és adja hozzá a következő kódot:
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Környezetben Unity - húzza a Player változó a fő kamera objektum a Player tárhely, hogy a két társítva egy másik. 
   
    ![][18]
6. Most már a Unity-szerkesztőben kattintson a Play és a Player golyó objektum elforgatása majd látják a kamera következő mozgása.  

### <a name="setting-up-the-play-area"></a>A Play terület beállítása
A rendszer az alábbi lépéseket a [Unity oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). A körülvevő az alapoktól, így a Player golyó objektum nem leadásához mozgása play területén falakat létre fogunk hozni. 

1. Kattintson a **létrehozás -> hozzon létre üres játék objektum ->** és adjon neki nevet **falvastagságát**
   
    ![][19]
2. Mért falvastagságát objektum - alatt hozzon létre egy új **3D objektum adatkocka ->** "Nyugati fali" néven. 
   
    ![][20]
3. Frissítés a **átalakító pozíció ->** és **átalakítási a méretezési ->** nyugati fali objektum. 
   
    ![][21]
4. Ismétlődő a nyugati fali létrehozásához egy **keleti fali** a módosított rendelkező átalakítási elhelyezése és méretezése. 
   
    ![][22]
5. Ismétlődő a keleti fali létrehozásához egy **északi fali** a módosított rendelkező átalakítási pozíció & skála. 
   
    ![][23]
6. A északi fali duplikált, és hozzon létre egy **Dél fali** a módosított rendelkező átalakítási pozíció & skála. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>A memóriából kiüríthető objektumok létrehozása
A rendszer az alábbi lépéseket a [Unity oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Néhány vonzó kinézetű objektumokat, amely a memóriából kiüríthető objektumokat, amely a Player golyó objektumhoz kell a "collect" való ütközés által készletét alkotó létre fogunk hozni. 

1. Hozzon létre egy új **3D adatkocka-objektum** és nevezze el a felvételi. 
2. Módosítsa a **átalakító -> Elforgatás** & **átalakítási a méretezési ->** a felvételi objektum. 
   
    ![][25]
3. Hozzon létre, és csatlakoztassa a **új C# parancsfájl** nevű **Rotator** felvételi objektumhoz. Ügyeljen arra, hogy a parancsfájlt helyezze a parancsfájlok mappában. 
   
    ![][26]
4. Nyissa meg szerkesztésre a parancsfájlt, és frissítse úgy, hogy a következő: 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. A tengely elforgatása most elérte a Play mód a Unity-szerkesztő és a felvételi objektum megjelenítése.
6. Hozzon létre egy új nevű **Prefabs** 
   
    ![][27]
7. Húzza a **a felvételi** objektumra, és a Prefabs mappába.
   
    ![][28]
8. Hozzon létre egy új **üres játék objektum** nevű **Pickups**. Pozícióját visszaáll az eredeti, és húzza a felvételi objektum játék objektum alatt.  
   
    ![][29]
9. Ismétlődő a **a felvételi** objektumra, és azt a terjednek a **Ground** körül objektum a **Player** objektum frissítésével a **Transform.Position tartozó X & Z** megfelelő értékeket. 
   
    ![][30]
10. Hozzon létre egy **új anyagokkal** nevű **a felvételi** és frissítése, hogy piros szín frissítésével a **Albedo tulajdonság** hasonló mi azt az, ami az alapoktól objektum frissítésére. 
    
    ![][31]
11. Az anyag alkalmazása 4 felvételi objektumait.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>A felvételi objektumok gyűjtése
A rendszer az alábbi lépéseket a [Unity oktatóanyag](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). A Windows Media Player frissítjük, hogy tud-e a "collect" a felvételi objektumok által való ütközés. 

1. Nyissa meg a **PlayerController** parancsfájl a Player objektum szerkesztésre csatolja, és frissíti a következőket:  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. Hozzon létre egy új **címke** nevű **válasszon mentése** (akkor meg kell egyeznie a parancsfájl a)  
   
    ![][33]
   
    ![][34]
3. Ez alkalmazza **címke** Prefab felvételi objektumhoz. 
   
    ![][35]
4. Engedélyezése **IsTrigger** a Prefab objektum jelölőnégyzetét.
   
    ![][36]
5. Kemény törzs hozzáadni a felvételi Prefab objektumhoz. A teljesítmény optimalizálása frissítjük a statikus collider használt dinamikus collider számára. 
   
    ![][37]
6. Végül ellenőrizze a **IsKinematic** a prefab vonatkozó tulajdonság. 
   
    ![][38]
7. Találati **lejátszása** a Unity a szerkesztőt, és meg fogja tudni ezt **tudják állítani egy golyó** játék azáltal, hogy az a billentyűkkel irány bemeneti Player objektum. 

### <a name="updating-the-game-for-mobile-play"></a>A game mobil Play frissítése
A fenti szakaszokban a Unity alapvető oktatóprogram kötött. Most már a játék abba, hogy mobileszköz rövid módosítja azt. Vegye figyelembe, hogy azt használja, amennyiben a teszteléshez játék bevitel a billentyűzetről. Most azt módosítja majd, hogy azt a telefonszámot, mozgásérzékelési azaz segítségével szabályozhatják a Windows Media player gyorsulásmérőt bemeneteként használja. 

Nyissa meg a **PlayerController** szerkesztésre parancsfájlt, és frissítse a **FixedUpdate** a Player objektum áthelyezése a gyorsulásmérő a mozgásérzékelési használandó módszert. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Ebben az oktatóanyagban egy egyszerű játék létrehozását a Unity megjelenik, és ez a játék tetszőleges eszközön telepítheti. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













