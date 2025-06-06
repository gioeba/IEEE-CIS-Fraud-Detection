# IEEE - CIS Fraud Detection
მოცემული კონკურსის მიზანია ონლაინ ტრანზაქციებში თაღლითობის აღმოჩენა. დატასეტი ძალიან დიდია (~500, 000 ტრანზაქცია) და დაბალანსებული არაა, თაღლითური ტრანზაქციები იშვიათია. ასევე, გამოწვწვებს გვიქმნის ბევრი გამოტოვებული მნიშვნეელობა და ანაონიმური ცვლადები, რაც Feature Engineering ს მნიშვნელოვანს ხდის. პრობლემის გადასაჭრელად გამოვცადე რამდენიმე მოდელი, რომელსაც შეუძლია ორობითი კლასიფიკაცია: Decision Tree, Random Forest, Gradient Boost, Ada Boost და XGBoost. Logistic Regression ამ ზომის ინფორმაციაზე ძალიან ნელი აღმოჩნდა. ამ მოდელების სხვადასხვა პარამეტრებით გატესტვის შემდეგ, საუკეთესო XGBoost აღმოჩნდა.

## რეპოზიტორიის სტრუქტურა
- model-experiment-decision-tree.ipynb: სამუშაო ფაილი Decision Tree მოდელისთვის.
- model-experiment-random-forest.ipynb: სამუშაო ფაილი Random Forest მოდელისთვის.
- model-experiment-radient-boost.ipynb: სამუშაო ფაილი Gradient Boost მოდელისთვის.
- model-experiment-ada-boost.ipynb: სამუშაო ფაილი Ada Boost მოდელისთვის.
- model-experiment-xgboost.ipynb: სამუშაო ფაილი XGBoost მოდელისთვის.
- model-inference.ipynb: ამ ფაილიდან საუკეთესო მოდელი (XGBoost) ეშვება ტესტ სეტზე.
- helper-classes.ipynb: დამხმარე კლასები, Feature Engineering და Preprocessing ისთვის.

## Feature Engineering
Feature Engineering ხდება helper-classes.ipynb ის FeatureEngineeringTransformer კლასში. კატეგორიული ცვლადები რიცხვითში გადაგვყავს OrdinalEncoder ის მეშვეობით, ხოლო თუ უცნობი კატეგორია შეგვხდება, ავტომატურად -1 ში გადაგვყავს დაქრაშვის ასარიდებლად. კატეგორიული ცვლადების შემთხვევაში გამოტოვებულ მნიშვნეელობებს ვავსებთ მოდით, ხოლო რიცხვითი ცვლადების შემთხვევაში მედიანით, რაც outlier-ებს უკეთესად ითვალისწინებს. თუ სვეტის 50% ან მეტი ცარიელია, მას აღარ ვითვალისწინებთ. ასევე TransactionDT დან ვქმნით ახალ feature-ებს: საათს, კვირის დღეს და დღეს(1-31), რის დასწავლასაც მოდელი უკეთესად შეძლებს თავდაპირველ ცვლადთან შედარებით. იფივეს ვშვებით DeviceInfo დან device_os-ს და device_ver-ის შექმნით, card1 დან და card2 დან card1/card2 + 1 და card1_addr1 ს რომელიც ამ ორ ცვლადს აერთებს. საბოლოოდ, ასევე დამატებით ვქმნით ტრანზაქციების რაოდენობას და საშუალო ტრანზაქციის ღირებულებას card1-ით.

## Feature Selection
Fetaure Selection-ისთვის, რადგან ჩვენს გამოყენებულ მოდელს ხის სტრუქტურა აქვთ, საშუალება გვაქვს, თითოეული გავუშვათ ორჯერ, SelectFromModel ის გამოყენებით, პირველი გაშვებისას გადავარჩევთ feature ებს და მეორე გაშვებისას მოდელს მხოლოდ დარჩენილებზე დავატრენინგებთ.

## Training
ტრენინგისთვის გამოვიყენე StratifiedKFold, სატრენინგო სეტი გავყავი 5 ნაწილად და თითოეულისთვის ხდება ვალიდაცია.გამოვცადე Decision Tree, Random Forest, Gradient Boost, Ada Boost და XGBoost. თითოეულის შემთხვევაში გამოვცადე ჰიპერპარამეტრთა სხვადასხვა კომბინაცია, მაგალითად მაქსიმალური სიღრმე, ბოლო ორისთვის გაშვებული მთვლელების რაოდენობა. საბოლოოდ ავარჩიე XGBoost, რადგან trainig-ზე ყველაზე მაღალი შედეგი ჰქონდა და test set ზე ძალიან არ დაცემულა, შესაბამისად overfit არ გვქონია. Ada Boost-ზე და Gradient Boost-ზე fit-ის გაზრდა ოდნავ მეტადაც იყო შესაძლებელი n_estimator და depth პარამეტრების გაზრდით, თუმცა გაშვებას ძალიან დიდი დრო უნდა და XGBoost-ის სიზუსტესთან მაინც ახლოს არ არიან, ამიტომ fit-ის ოდნავ გასაზრდელად run-ები აღარ დავლოგე. Random Forest ზე fit-ის მეტად გაზრდა overfit-ში გადაგვიყვანს, ხოლო decision tree ოპტიმალურ წერტილშია, სიღრმის გაზრდითაც და შემცირებითაც სიზუსტე იკლებს, მაგრამ რადგან 1-თან ახლოს არაა, overfit არ გვაქვს.

## Mlflow Tracking
https://dagshub.com/gioeba/IEEE-CIS-Fraud-Detection.mlflow/#/experiments/0?searchFilter=&orderByKey=attributes.start_time&orderByAsc=false&startTime=ALL&lifecycleFilter=Active&modelVersionFilter=All+Runs&datasetsFilter=W10%3D
თითოეული მოდელისთვის შევქმენი ცალკე ექსპერიმენტი, ასევე ცალკე ექსპერიმენტია შექმნილი Feature Engineering და Preprocessing ისთვის, რათა მათი რამდენიმეგან გამოყენება შემძლებოდა კოდის გამეორების გარეშე. თითოეული მოდელისთვის შენახულია Area under ROC curve და ის ჰიპერპარამეტრები, რაც ამ გაშვებისას გავუწერე(მაქსიმალური სიღმე, მთვლელების რაოდენობა). საუკეთესო მოდელმა, XGBoost მა, training-ზე აჩვენა ~0.92 ფართობი, რაც test set ზე მხოლოდ მცირედით შემცირდა 0.89-მდე. 
