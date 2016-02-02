美國交通部收集的 TranStats 資料所包含的客機航班準點率資料準時 (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">準時</a>)。<p></p>此資料集涵蓋的時間週期年 4 月到 10 月 2013年。之前上傳到 Azure ML Studio 中，資料集處理，如下所示:<ul><li>資料集已經過篩選，涵蓋只 70 個最繁忙的機場美國大陸</li><li>已取消的航班已標示為誤點達 15 分鐘以上</li><li>已篩選掉 Diverted 航班</li><li>已選取下列資料行: 年、 月、 DayofMonth、 DayOfWeek、 電信業者、 OriginAirportID、 DestAirportID、 CRSDepTime、 DepDelay、 DepDel15、 CRSArrTime、 ArrDelay、 ArrDel15已取消</li></ul>




