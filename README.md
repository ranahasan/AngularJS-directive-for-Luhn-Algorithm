# AngularJS-directive-for-Luhn-Algorithm
# app.js
```
var app = angular.module('plunker', []);

app.controller('MainCtrl', function($scope) {
  $scope.myNumber = null;
  $scope.config = {
    sbMin: 0,
    sbMax: 200,
    sbPrecision: 2,
    sbMaxPrecision: 2
  }
});

function countDecimalPlaces(value){
  var decimalPos = String(value).indexOf('.');
  if(decimalPos === -1){
    return 0;
  }else{
    return String(value).length - decimalPos -1;
  }
}

app.directive('sbPrecision', function(){
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, element, attributes, ngModel){
      var precision = attributes.sbPrecision;
      
      function setPrecision(){
        var value = ngModel.$modelValue;
        
        //since this is just a mask, don't hide decimal values that
        //go beyond our precision and don't format empty values
        if(value && !isNaN(value) && countDecimalPlaces(value) <= precision){
          var formatted = Number(value).toFixed(precision);
          ngModel.$viewValue = formatted;
          ngModel.$render();
        }
      }
      
      element.bind('blur', setPrecision);
      setTimeout(setPrecision, 0); //after initial page render
    }
    
  };
})

app.directive('sbMin', function(){
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, element, attributes, ngModel){

      function minimum(value){
        if(!isNaN(value)){
          var validity = Number(value) >= attributes.sbMin;
          ngModel.$setValidity('min-value', validity)
        }
        return value;
      }
      
      ngModel.$parsers.push(minimum); 
      ngModel.$formatters.push(minimum); 
    }
    
  };
})

app.directive('sbMax', function(){
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, element, attributes, ngModel){

      
      function maximum(value){
        if(!isNaN(value)){
          var validity = Number(value) <= attributes.sbMax;
          ngModel.$setValidity('max-value', validity);
        }
        
        return value;
      }
      
      ngModel.$parsers.push(maximum);
      ngModel.$formatters.push(maximum); 
    }
    
  };
})

app.directive('sbNumber', function(){
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, element, attributes, ngModel){

      
      function validateNumber(value){
        var validity = !isNaN(value);
        ngModel.$setValidity('number', validity)
        return value;
      }
      
      ngModel.$parsers.push(validateNumber);
      ngModel.$formatters.push(validateNumber); 
    }
    
  };
})

app.directive('sbMaxPrecision', function(){
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, element, attributes, ngModel){
      
      function maxPrecision(value){
        if(!isNaN(value)){
          var validity = countDecimalPlaces(value) <= attributes.sbMaxPrecision;
          ngModel.$setValidity('max-precision', validity);
        }
        
        return value;
      }
      
      ngModel.$parsers.push(maxPrecision);
      ngModel.$formatters.push(maxPrecision); 
    }
    
  };
})
```

# style.css
```
.error{
  background: red;
  color: white;
  padding: 5px 10px;
}
input{
  border: 1px solid gray;
  padding: 5px 15px;
  border-radius: 5px;
  font-size: 20px;
  font-weight: bold;
}
```

# index.html
```
<!DOCTYPE html>
<html ng-app="plunker">

<head>
  <meta charset="utf-8" />
  <title>AngularJS Luhn Check directive</title>
  <script>
    document.write('<base href="' + document.location + '" />');
  </script>
  <link rel="stylesheet" href="style.css" />
  <script src="http://code.angularjs.org/1.2.25/angular.js"></script>
  <script src="app.js"></script>
</head>

<body ng-controller="MainCtrl">
<h2>Luhn Validator</h2>
  <div ng-form='myForm'>
    <input type='text' ng-model='myNumber' name='number' luhn-check />
    <div ng-show="myForm.number.$error['luhn-check']" class='error'>Error: failed Luhn check</div>
  </div>
  
  <div>
  <h4>Valid numbers</h4>
    <ul>
      <li>4242424242424242</li>
      <li>4012888888881881</li>
      <li>5200828282828210</li>
      <li>378282246310005</li>
      <li>6011111111111117</li>
      <li>30569309025904</li>
    </ul>
  </div>
</body>

</html>
```
