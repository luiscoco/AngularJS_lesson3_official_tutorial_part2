# AngularJS Official Tutorial (Part 2): explained step by step

## 1. Step 7: XHR & Dependency Injection

Let's fetch a larger dataset from our server using one of AngularJS's **built-in services** called **$http**

There is now a list of 20 phones, loaded from the server

We have to add a new folder with the phones list

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/7e0e8c73-301d-4002-80df-8aad2058394a)

```json
[
    {
        "age": 0, 
        "id": "motorola-xoom-with-wi-fi", 
        "imageUrl": "img/phones/motorola-xoom-with-wi-fi.0.jpg", 
        "name": "Motorola XOOM\u2122 with Wi-Fi", 
        "snippet": "The Next, Next Generation\r\n\r\nExperience the future with Motorola XOOM with Wi-Fi, the world's first tablet powered by Android 3.0 (Honeycomb)."
    }, 
    {
        "age": 1, 
        "id": "motorola-xoom", 
        "imageUrl": "img/phones/motorola-xoom.0.jpg", 
        "name": "MOTOROLA XOOM\u2122", 
        "snippet": "The Next, Next Generation\n\nExperience the future with MOTOROLA XOOM, the world's first tablet powered by Android 3.0 (Honeycomb)."
    },
...
]
```

This is the **source code** modifications

**app/phone-list/phone-list.component.js**

```javascript
'use strict';
// Register `phoneList` component, along with its associated controller and template
angular.
  module('phoneList').
  component('phoneList', {
    templateUrl: 'phone-list/phone-list.template.html',
    controller: ['$http', function PhoneListController($http) {
      var self = this;
      self.orderProp = 'age';

      $http.get('../phones/phones.json').then(function(response) {
        self.phones = response.data;
      });
    }]
  });
```

**app/phone-list/phone-list.component.spec.js**

```javascript
'use strict';
describe('phoneList', function() {
  // Load the module that contains the `phoneList` component before each test
  beforeEach(module('phoneList'));

  // Test the controller
  describe('PhoneListController', function() {

    var $httpBackend, ctrl;

    // The injector ignores leading and trailing underscores here (i.e. _$httpBackend_).
    // This allows us to inject a service and assign it to a variable with the same name
    // as the service while avoiding a name conflict.
    beforeEach(inject(function($componentController, _$httpBackend_) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('../phones/phones.json')
                  .respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      ctrl = $componentController('phoneList');
    }));

    it('should create a `phones` property with 2 phones fetched with `$http`', function() {
      expect(ctrl.phones).toBeUndefined();

      $httpBackend.flush();
      expect(ctrl.phones).toEqual([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    });

    it('should set a default value for the `orderProp` property', function() {
      expect(ctrl.orderProp).toBe('age');
    });

  });
});
```

**e2e-tests/scenarios.js**

```javascript
'use strict';
// AngularJS E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing
describe('PhoneCat Application', function() {
  describe('phoneList', function() {
    beforeEach(function() {
      browser.ignoreSynchronization = true;  // Disable Angular synchronization
      browser.get('http://127.0.0.1:5500/app/index.html');
    });

    it('should filter the phone list as a user types into the search box', function() {
      var EC = protractor.ExpectedConditions;
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));

      browser.wait(EC.presenceOf(query), 5000, 'Query input taking too long to appear in the DOM');

      expect(phoneList.count()).toBe(20);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });

    it('should be possible to control phone order via the drop-down menu', function() {
      var EC = protractor.ExpectedConditions;
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));

      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }

      browser.wait(EC.presenceOf(queryField), 5000, 'Query input taking too long to appear in the DOM');
      browser.wait(EC.presenceOf(orderSelect), 5000, 'Order select taking too long to appear in the DOM');

      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter
      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);

      nameOption.click();
      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });
  });
});
```

Now let's **run the application**

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/3acad084-a0a5-486f-9a54-e5b6dafe1bfb)

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/9455d662-6853-4ef8-a3b3-614c3073f3b4)

Also we have to **run the tests**

Unit Tests

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/7dca1ece-5bb7-478a-9728-4c9592343924)

E2E Tests

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/af370fb8-a3e9-42d6-9ea7-122f886ef11f)

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/743f88fa-c8be-453e-9fb5-3e9d9caf2d7b)

## 2. Step 8: Templating Links & Images

In this step, we will **add thumbnail images** for the phones in the phone list, and links that, for now, will go nowhere

We first add the phone images list to our application

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/267f4117-6679-4c89-b25f-6d44362dd9fb)

Now we introduce some changes in the code

**app/app.css**

```css
body {
    padding-top: 20px;
  }

.phones {
  list-style: none;
}

.phones li {
  clear: both;
  height: 115px;
  padding-top: 15px;
}

.thumb {
  float: left;
  height: 100px;
  margin: -0.5em 1em 1.5em 0;
  padding-bottom: 1em;
  width: 100px;
}
```

**app/phone-list/phone-list.template.html**

```javascript
<div class="col-md-2">
  <!--Sidebar content-->

  <p>
    Search:
    <input ng-model="$ctrl.query" />
  </p>

  <p>
    Sort by:
    <select ng-model="$ctrl.orderProp">
      <option value="name">Alphabetical</option>
      <option value="age">Newest</option>
    </select>
  </p>

</div>
<div class="col-md-10">
  <!--Body content-->

  <ul class="phones">
    <li ng-repeat="phone in $ctrl.phones | filter:$ctrl.query | orderBy:$ctrl.orderProp" class="thumbnail">
      <a href="#!/phones/{{phone.id}}" class="thumb">
        <img ng-src="{{phone.imageUrl}}" alt="{{phone.name}}" />
      </a>
      <a href="#!/phones/{{phone.id}}">{{phone.name}}</a>
      <p>{{phone.snippet}}</p>
    </li>
  </ul>

</div>
</div>
</div>
```

**e2e-tests/scenarios.js**

```javascript
'use strict';
// AngularJS E2E Testing Guide:
// https://docs.angularjs.org/guide/e2e-testing
describe('PhoneCat Application', function() {
  describe('phoneList', function() {
    beforeEach(function() {
      browser.ignoreSynchronization = true;  // Disable Angular synchronization
      browser.get('http://127.0.0.1:5500/app/index.html');
    });
    it('should filter the phone list as a user types into the search box', function() {
      var phoneList = element.all(by.repeater('phone in $ctrl.phones'));
      var query = element(by.model('$ctrl.query'));
      expect(phoneList.count()).toBe(20);
      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);
      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(8);
    });
    it('should be possible to control phone order via the drop-down menu', function() {
      var queryField = element(by.model('$ctrl.query'));
      var orderSelect = element(by.model('$ctrl.orderProp'));
      var nameOption = orderSelect.element(by.css('option[value="name"]'));
      var phoneNameColumn = element.all(by.repeater('phone in $ctrl.phones').column('phone.name'));
      function getNames() {
        return phoneNameColumn.map(function(elem) {
          return elem.getText();
        });
      }
      queryField.sendKeys('tablet');   // Let's narrow the dataset to make the assertions shorter
      expect(getNames()).toEqual([
        'Motorola XOOM\u2122 with Wi-Fi',
        'MOTOROLA XOOM\u2122'
      ]);
      nameOption.click();
      expect(getNames()).toEqual([
        'MOTOROLA XOOM\u2122',
        'Motorola XOOM\u2122 with Wi-Fi'
      ]);
    });

    it('should render phone specific links', function() {
      var query = element(by.model('$ctrl.query'));
      query.sendKeys('nexus');

      element.all(by.css('.phones li a')).first().click();
      expect(browser.getCurrentUrl()).toContain('index.html#!/phones/nexus-s');
    });

  });

});
```

We run the application

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/5d664c58-d8eb-4b07-916f-4af2628b66b5)

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/aefe246a-16b5-460e-b9ec-d4c4cdc43be9)

We run the Test cases

Unit Tests

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/2cb1aab9-a3dd-4928-ab4d-d89abb44f5d9)

E2E Tests

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/5d118f3e-5fd1-4dfd-ba4d-de0c888469e6)

![image](https://github.com/luiscoco/AngularJS_lesson3_official_tutorial_part2/assets/32194879/05c9da49-e57d-4336-9f71-5323a19f89a7)

## 3. Step 9: Routing & Multiple Views



## 4. 




