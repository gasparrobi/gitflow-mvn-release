<p align="center">
<img src= "./images/protractor-typescript-cucumber.png" height=300 alt="titleImage.png"/>
</p>

<p align="center">
   <i><strong>This project demonstrates the basic protractor-cucumber-typescript framework project setup for GURU testing.
</strong></i>
<p>


---

### Features
* No typings.json or typings folder, they have been replaced by better **'@types'** modules in package.json.
* ts-node(typescript execution environment for node) in cucumberOpts. 
* All scripts written with > Typescript2.0 & Cucumber2.0.
* Neat folder structures with transpiled js files in separate output folder.
* Page Object design pattern implementation.
* Extensive hooks implemented for BeforeFeature, AfterScenarios etc.
* Screenshots on failure feature scenarios.


#### Setup Scripts
* Go inside the folder and run following command from terminal/command prompt
```
npm install 
```
* All the dependencies from package.json and ambient typings would be installed in node_modules folder.

#### Run Scripts

* First step is to fire up the selenium server which could be done in many ways,  **webdriver-manager** proves very handy for this.The below command should download the **chrome & gecko driver** binaries locally for you!

```
npm run webdriver-update
``` 

* Then you should start your selenium server!
```
npm run webdriver-start
```

* The below command would create an output folder named 'typeScript' and transpile the .ts files to .js.
```
npm run build
```

* Now just run the test command which launches the Chrome Browser and runs all tests from all feature files which ar tagged as @test.
```
npm test
```

* test running options:
```
// to run all the tests only from a specific feature file use the file name without the .feature extension. (ex: myTest.feature)
npm test -- --feature=myTest

// run test only with a specific tag on them. (ex: @happy)
npm test -- --tags=@happy

// for multiple tags use a comma (,)
npm test -- --tags=@happy,@fast
```

---


#### Writing Features
```
Feature: To search typescript in google

  @test @google
  Scenario: Typescript Google Search
    Given I am on google page
    When I type "Typescript"
    Then I click on search button
    Then I clear the search text
```
#### Writing Step Definitions
    
```
import { browser } from "protractor";
import { SearchPageObject } from "../pages/searchPage";
const { Given } = require("cucumber");
const chai = require("chai").use(require("chai-as-promised"));
const expect = chai.expect;

const search: SearchPageObject = new SearchPageObject();

Given(/^I am on google page$/, async () => {
    await expect(browser.getTitle()).to.eventually.equal("Google");
});
```

#### Writing Page Objects
```
import { $ } from "protractor";

export class SearchPageObject {
    public searchTextBox: any;
    public searchButton: any;

    constructor() {
        this.searchTextBox = $("#lst-ib");
        this.searchButton = $("input[value='Google Search']");
    }
}
```
#### Cucumber Hooks
Following method takes screenshot on failure of each scenario
```
After(async function(scenario) {
    if (scenario.result.status === Status.FAILED) {
        // screenShot is a base-64 encoded PNG
         const screenShot = await browser.takeScreenshot();
         this.attach(screenShot, "image/png");
    }
});
```
#### CucumberOpts Tags
Following configuration shows to call specific tags from feature files
```
cucumberOpts: {
    compiler: "ts:ts-node/register",
    format: "json:./reports/json/cucumber_report.json",
    require: ["../../stepdefinitions/*.ts", "../../stepdefinitions/**/*.ts", "../../support/*.ts"],
    strict: false,
    tags: getTags()
},
```

# List of exisiting steps
 
---

### common (angular)
`stepdefinitions/common/commonSteps.ts`

- `"..." feliratú gombra kattintok` - `...`
- `"..." feliratú gombot látom a felületen` - `...` feliratú gombot keres a felületen, ennek hiányában a teszt sikertelen
- `bezárom az összes böngésző fület` - 
- `"..." db böngésző fület látok a felületen` - 
- `"..." űrlapra kerülök` - az adott böngésző fül felirata tartalmazza a `...` substringet

### keret specifikus lépések
`stepdefinitions/keret/*.ts`

- `a "..." felhasználó be van jelentkezve` - Keret belépési felületen a `...` felhasználó beléptetése `Tesztelo1` jelszóval. ha már a Keretrendszer felületen vagyunk ez a lépés automatikusan kimarad.

Személyes AdatMódisítás folyamat indítása:
- `keresés felületen kiválasztom a "..." ügyfelet` - ügyfélazonosító-ra indított keresés
- `indítok egy Személyes adat módosítása ügyet "..." forrással` - szemadat indítása `Személyes, Telefon, Posta` vagy `Email` forrással. Ügy indítás után vissza kerülünk a feladatok nézetre
 
Folyamat indítása szerződésre:
- `keresés felületen megkeresem a "..." ügyfélhez tartozó "..." számú szerződést` - ügyfélazonosítóra és szerződésszámra indított keresés
- `a kiválasztott szerződésre indítok egy ügyet a következő adatokkal. Ügy csoport: "...", Ügy típus: "...", Forrás: "...", Jogviszony típusa: "..."` - ügy indítás után vissza kerülünk a feladatok nézetre

Feladatok nézet:
- `kiválasztom a legfrissebb ügyet` - a legfrisseb ügyön az Űrlap linkre kattintunk, majd át kerülünk egy új böngésző fülre


### Személyes Adatmódosítás specifikus lépések

- `kitöltöm az értesítési adatokat, az értesítési módot "..." értékre, a címet pedig "..." értékre állítom` - értesítési módok: Posta vagy Email
- `módosítom a személyiigazolvány számát a "..." értékre` -
- `dokumentumcsatolás felületen vagyok` -
- `csatolom a dokumentumokat` -

---

### Szemadat példa folyamat
```$cucumber
Feature: Személyes Adat Módosítás folyamat

    @test @szemAdat
    Scenario: személyiigazolvány szám módosítás - SzemAdat folyamat Személyes forrással ([-] teszt)
        Given a "tesztuser_71401_SZ" felhasználó be van jelentkezve
        And keresés felületen kiválasztom a "798762407" ügyfelet
        And indítok egy Személyes adat módosítása ügyet "Személyes" forrással
        And kiválasztom a legfrissebb ügyet
        Then "Személyes adatmódosítás - kérelemrögzítés" űrlapra kerülök
        When kitöltöm az értesítési adatokat, az értesítési módot "Posta" értékre, a címet pedig "5667 Magyarbánhegyes, Béke utca 30" értékre állítom
        And módosítom a személyiigazolvány számát a "AJ532980" értékre
        And "Tovább" feliratú gombra kattintok
        Then dokumentumcsatolás felületen vagyok
        And "Bejelentő generálás" feliratú gombot látom a felületen
        When "Bejelentő generálás" gombra kattintok
        Then "Tovább" feliratú gombot látom a felületen
        When csatolom a dokumentumokat
        And "Tovább" feliratú gombra kattintok
        Then "1" db böngésző fület látok a felületen
```
- `` -




