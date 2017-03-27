
Mivel az Azure Functions gyorsindítók működő kódokat tartalmaznak, az új függvények azonnal tesztelhetők.

1. A **Develop** (Fejlesztés) lapon tekintse át a **Code** (Kód) ablakot, és figyeljen arra, a megadott kód egy olyan HTTP-kérést vár, amelyben a *name* (név) érték vagy az üzenet törzsében, vagy egy lekérdezési karakterláncban van továbbítva. Ha a függvény fut, a rendszer ezt az értéket a válaszüzenetben adja vissza. Az alábbi példa egy JavaScript-függvény.
   
2. Kattintson a **Test (Tesztelés)** elemre a függvény beépített HTTP-tesztkérési panelének megjelenítéséhez.
 
    ![](./media/functions-quickstart-test/function-app-develop-tab-testing.png)

3. A **Request body** (Kéréstörzs) szövegdobozban, módosítsa a *name* (név) tulajdonságot a saját nevére, és kattintson a **Run** (Futtatás) parancsra. Látni fogja, ahogy egy próba HTTP-kérés aktiválja a végrehajtást, információ íródik a naplókba, és a „hello...” válasz megjelenik a **Kimenet** mezőben. 

4. Ugyanannak a függvénynek egy HTTP-teszteszközből vagy egy másik böngészőablakból történő aktiválásához másolja ki a **Function URL** (Függvény URL) értéket a **Develop** (Fejlesztés) lapról, és illessze be az eszköz vagy a böngésző címsorába. Az URL-címhez fűzze hozzá a `&name=yourname` lekérdezési karakterláncértéket, és hajtsa végre a kérést. Vegye figyelembe, hogy ugyanez az információ lesz a naplókba írva, és ugyanez a sztring jelenik meg a válaszüzenet törzsében is.

    ![](./media/functions-quickstart-test/function-app-browser-testing.png)
