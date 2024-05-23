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
  });
});
```

## 2. 


## 3. 



## 4. 




