
الداتا عبارة عن 100000 صف و20 عمود والطلبات من سنة 2020 لحد 2024

 باستخدام Column Profiling راجعت كل الأعمدة ولقيت إن مفيش قيم فاضية ولا صفوف مكررة وكمان كل OrderID مختلف عن التاني ومعادلة TotalAmount سليمة

ثانيا ظبطت أنواع البيانات
خليت OrderDate من نوع Date عشان نتعامل معاه كتاريخ وQuantity من نوع Whole Number عشان الكمية عدد صحيح

وخليت UnitPrice وTax وShippingCost وTotalAmount من نوع Fixed Decimal وDiscount من نوع Decimal وباقي الأعمدة Text

وعملت Trim وClean  عشان أشيل أى مسافات أو نصوص زيادة 

ثالثا شيلت 4 أعمدة

شيلت CustomerName لأن نفس CustomerID كان بيظهر بأكتر من اسم 

وحذفت Brand لأن كل منتج ظاهر مع العشر براندات بشكل عشوائي

وشيلت City وState لأن البيانات مش متوافقة ونفس المدينة ممكن تظهر في دول مختلفة


عشان كده مش هحطها في Dim_Product وهخليها في ال Fact مع جدول Dim_Category منفصل

رابعا ضفت 5 أعمدة محسوبة

أول عمود GrossAmount وده الكمية مضروبة في سعر الوحدة 

تاني عمود DiscountAmount وده قيمة الخصم الفعلية 

تالت عمود NetSales وده قيمة المبيعات بعد الخصم وقبل الضريبة والشحن 

رابع عمود PriceBand وده بيصنف المنتجات حسب السعر


خامسا جهزت Orders_Clean كمصدر لباقي الجداول

قفلت Enable Load عليه عشان يفضل مصدر أساسي بس وكل جدول من ال Fact وال Dimensions هنعمله باستخدام Reference

وده معناه إن لو عدلنا أي حاجة في مرحلة ال Cleaning باقي الجداول هتتحدث تلقائي مع التعديل

وخطوة اضافية جبت ملف خارجى هو ال product mapping عشان يكون كل product فى ال category الخاص بيه  ، بدل ما كان فى product ليه كذا category

الخطوة الجاية و هو ما يخص ال data modelingهنعمل Fact_Orders من غير ProductName وOrderDate وهنعمل 7 Dimensions وهم Product وCategory وCustomer وSeller وGeography وPayment وOrderStatus
