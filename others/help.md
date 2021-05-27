# Help!!

```java
package be.telenet.codoogle;

class Vehicle {

}

class Motorbike extends Vehicle {

}

class Car extends Vehicle {

}

class Truck extends Vehicle {

}

public class Washing {

	private Vehicle vehicle;
	private boolean isInternal;
	private boolean isExternal;

	public Washing(Vehicle vehicle) {
		this.vehicle = vehicle;
	}
	
	public Washing(Vehicle vehicle,boolean isInternal, boolean isExternal ) {
		this.vehicle = vehicle;
		this.isInternal=isInternal;
		this.isExternal=isExternal;
	}

	public double washingAmount(Motorbike bike) {
		return 0;
	}

	public double washingAmount(Car car, boolean isInternal, boolean isExternal) {
		return 0;
	}

	public double washingAmount(Truck truck, boolean isInternal, boolean isExternal) {
		return 0;
	}

}

```

