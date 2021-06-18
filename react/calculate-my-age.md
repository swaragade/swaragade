---
description: "I always seem to forget I old I am \U0001F60B, and end up counting my age on my fingers. So made this react app to calculate your age."
---

# Calculate my age

Find the app at : [https://github.com/swaragade/age-calculator](https://github.com/swaragade/age-calculator)

Below is part of that app which actually calculates the age

```javascript
var daysArray = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
var dob = new Date(1994, 01, 14);
var current_day = new Date(2021, 00, 15);
var today = new Date(
  current_day.getFullYear(),
  current_day.getMonth(),
  current_day.getDate()
);
var years = 0;
var months = 0;
var days = 0;

if (dob.getFullYear() <= today.getFullYear()) {
    console.log("dob= ",dob.getDate() ,"| today= ", today.getDate());
    console.log("dob month= ",dob.getMonth() ,"| current month= ", today.getMonth());
    console.log("dob year= ",dob.getFullYear() ,"| current year= ", today.getFullYear());
  // when user is older than current month
  if (today.getMonth() > dob.getMonth()) {
    years = today.getFullYear() - dob.getFullYear();
    if (today.getDate() < dob.getDate()) {
      months = today.getMonth() - dob.getMonth() - 1;
      days = daysArray[(today.getMonth()-1+12)%12] - dob.getDate() + today.getDate();
    } else {
      months = today.getMonth() - dob.getMonth();
      days = today.getDate() - dob.getDate();
    }
  } else if (today.getMonth() < dob.getMonth()) {
    // when user is younger than current month
    years = today.getFullYear() - dob.getFullYear() - 1;
    if (today.getDate() < dob.getDate()) {
      months = dob.getMonth() - today.getMonth() - 1;
      days = daysArray[(today.getMonth()-1+12)%12] - dob.getDate() + today.getDate();
    }else if(today.getDate() > dob.getDate()){
        months = dob.getMonth() - today.getMonth();
        days = today.getDate() - dob.getDate();
    } 
    else {
      months =  dob.getMonth() -today.getMonth();
      days = today.getDate() - dob.getDate();
    }
  } else {
      // same month
    if (dob.getDate() > today.getDate()) {
      years = today.getFullYear() - dob.getFullYear();
      months = dob.getMonth() - today.getMonth();
      days = daysArray[(today.getMonth()-1+12)%12] - dob.getDate() + today.getDate();
    } else if(dob.getDate() < today.getDate()) {
        console.log('today is greater');
        years = today.getFullYear() - dob.getFullYear()-1;
        months = 0;
        days = today.getDate() -dob.getDate();
    }else{
        // when dates are equal
        years = today.getFullYear() - dob.getFullYear();
    }
  }
} else {
  console.log("its a future date");
}
console.log();
console.log(
  "You are ",
  years,
  " years ",
  months,
  " months and ",
  days,
  " days old"
);

```

