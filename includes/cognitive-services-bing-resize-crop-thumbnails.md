Néhány Bing válaszokat adhat URL-címeket a Bing által kiszolgált miniatűr képét. Előfordulhat, hogy átméretezése és levágása miniatűr képét. 

> [!NOTE]
> Győződjön meg arról a méret a miniatűr vágás adja meg a keresési forgatókönyv és és tiszteletben tartják a harmadik fél jogainak, a Bing keresési API-JÁNAK használatával szükség szerint jeleníti meg a követelményeknek.


Kép átméretezése, tartalmaznia a szélesség és a magassági lekérdezési paramétert a Miniatűr URL-címben. Adja meg a szélessége és magassága képpontban. Példa:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Ha a lemezképet, az eredeti oldalarányok megmarad. Az eredeti oldalarányok fenntartása érdekében a kép szegélyének fehér kitöltés adhatók hozzá. Például ha egy 200 x 200 480 x 359 lemezképet vágás nélkül, a teljes szélesség lemezképét tartalmazza, de magasságát tartalmazza a top és a lemezkép alján kitöltési fehér 25 képpontban megadva. Ugyanez igaz lenne, ha a lemezkép: 359 x 480, kivéve a bal és jobb oldali szegély tartalmazná fehér kitöltés. Ha a kép levágja fehér kitöltés nem kerül.  

 
Az alábbi képen egy miniatűr képére (480 x 300) eredeti méretét.  
  
![Eredeti fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Az alábbi képen látható az átméretezést 200 x 200-zal. Az eredeti oldalarányok megmarad, valamint a felső és alsó határok helyeken fehér (a fekete szegély megtalálható a kitöltési megjelenítendő).  
  
![Átméretezett fekvő kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Ha dimenziókat, amelyek nagyobbak, mint a kép eredeti szélességének és magasságának ad meg, a lemezképet a bal és a felső szegély a fehér van kiegészítve.  
  
Kép levágása, adja meg a c (vágás) lekérdezési paraméter. A megadható értékek a következők.  
  
- 4&mdash;vak arány  
- 7&mdash;intelligens arány  
  
Ha Ön kérte intelligens arány vágás (c = 7), a lemezképet a rendszer levágja a lemezkép operációs rendszerének tartományban, a középre passzív a lemezkép operációs rendszerének oldalarányának megőrzésével. A tartományban, az a terület a kép, amely meghatározza a Bing, a legtöbb importálási részeket tartalmazza. Az alábbiakban látható egy példa terület iránt.  
  
![Az érdeklődési régióban](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Ha egy kép átméretezése, és kérjen intelligens arány vágás, a lemezkép csökkenteni kell a kért méret a oldalarányának megőrzésével. A lemezképet a rendszer majd levágja a átméretezett méretei alapján. Például ha az átméretezett szélessége nagyobb, mint a magasság, a lemezkép levágja a bal és jobb a tartományban, közepére. Ellenkező esetben a kép rendszer levágja a felső és alsó részén a tartományban, közepére.  
  
 
Az alábbiakban látható a lemezkép használatával intelligens arány vágás 200 x 200 csökken.  
  
![Fekvő kép levágja a 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Az alábbiakban látható a lemezkép használatával intelligens arány vágás 200 x 100 csökken.  
   
![Fekvő kép levágja a 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Az alábbiakban látható a lemezkép használatával intelligens arány vágás 100 x 200 csökken.  
  
![Fekvő kép levágja a 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Bing a lemezkép operációs rendszerének tartományban, nem tudja megállapítani, ha a Bing vak arány vágás használja.  
  
Ha Ön kérte, vak arány vágás (c = 4), a Bing a következő szabályokat használ a kép levágása.  
  
- Ha (eredeti képének szélessége / eredeti kép magassága) < (a kért kép szélessége / kért kép magassága), a kép sarok bal felső, alsó levágja a mérése történik.  
- Ha (eredeti képének szélessége / eredeti kép magassága) > (a kért kép szélessége / kért kép magassága), a rendszerkép mért és levágja a bal és jobb.  



Az alábbiakban látható egy 225 x 300 álló lemezképe.  
  
![Eredeti napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Az alábbiakban látható a lemezkép használatával vak arány vágás 200 x 200 csökken. A kép a bal felső sarokból alatt levágja a kép alsó részén eredményezve mérése történik.  
  
![Levágja a 200 x 200 napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Az alábbiakban látható a lemezkép használatával vak arány vágás 200 x 100 csökken. A kép a bal felső sarokból alatt levágja a kép alsó részén eredményezve mérése történik.  
  
![Levágott és 200 x 100 napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Az alábbiakban látható a lemezkép használatával vak arány vágás 100 x 200 csökken. A kép a bal és jobb részei alatt levágja eredményezve központból mérése történik.  
  
![Levágja a 100 x 200 napraforgó kép](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

