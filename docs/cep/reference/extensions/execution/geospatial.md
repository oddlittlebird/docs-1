# Geo Spatial
This extension provides geo data related functionality such as checking whether a given geo coordinate is within a predefined geo-fence, finding distance between 2 geo coordinates etc.

## Features

* **[distance (Function)](#distance)**

    This method gives the distance between two geo locations in meters.
    
* **[intersects (Function)](#intersects)**

    This function can be called using two sets of parameters.
    First method will return true if the incoming event geo.json.geometry intersects the given geo.json.geometryFence else false.
    Second method will return true if the location pointed by longitude and latitude intersects the given geo.json.geometryFence else false 
    
* **[contains (Function)](#contains)**

    This function can be call using two sets of parameters.
    This will returns true if the location specified in terms of longitude and latitude is within the geo.json.geometry.fence.
    Or returns true if the geo.json.geometry is within the geo.json.geometry.fence.Returns false otherwise 
    
* **[withinDistance (Function)](#withinDistance)**

    This function can be call using two sets of parameters.
    First method will returns true if the location specified in terms of longitude and latitude is within distance of the geo.json.geometry.fence. Returns false otherwise.
    Second method will return true if the area given by geo.json.geometry is within distance of the geo.json.geometry.fence. 
    
* **[disjoint (Function)](#disjoint)**

    This function can be call using two sets of parameters.
    First method will returns true if the incoming event geo.json.geometry is disjoint from the given geo.json.geometryFence. Returns false otherwise.
    Second method will return true if the location pointed by longitude and latitude is disjoint from the given geo.json.geometryFence. Returns false otherwise.

* **[touches (Function)](#touches)**

    This function can be call using two sets of parameters.
    First method will returns true if the incoming event geo.json.geometry touches the given geo.json.geometryFence. Returns false otherwise.
    Second method will return true if the location pointed by longitude and latitude touches the given geo.json.geometryFence. Returns false otherwise.

* **[overlaps (Function)](#overlaps)**

    This function can be call using two sets of parameters.
    First method will returns true if the incoming event geo.json.geometry overlaps the given geo.json.geometryFence. Returns false otherwise.
    Second method will return true if the location pointed by longitude and latitude overlaps the given geo.json.geometryFence. Returns false otherwise.


* **[equals (Function)](#equals)**

    This function can be call using two sets of parameters.
    First method will returns true if the incoming event geo.json.geometry equals the given geo.json.geometryFence. Returns false otherwise.
    Second method will return true if the location pointed by longitude and latitude equals the given geo.json.geometryFence. Returns false otherwise.
    
* **[closestPoints (Stream Function)](#closestPoints)**

    This will return the closest geo point to the geo.json.geometry.fence 
    
* **[locationApproximate (Stream Function)](#locationApproximate)**

    Geo Location Approximation compute the average location of the locationRecorder using the collection iBeacons which the location recorder resides. 

* **[crosses (Stream Processor)](#crosses)**

    This will return true when the the specified object of which the location is specified in terms of longitude and latitude crosses the geographic location specified in         geo.json.geometry.fence. Returns false when the object crosses out of the location specified in geo.json.geometry.fence
 Or Returns true when the object (i.e. geo.json.geometry) crosses the specified geographic location (i.e. geo.json.geometry.fence). Returns false when the object crosses out of  geo.json.geometry.fence. 
 
 * **[proximity  (Stream Processor)](#proximity)**

   This will return true when two objects (specified in terms of longitude and latitude) are within the specified radius to another object. Returns false when the specified object moves out of the specified radius. The proximityWith optional attribute indicates the ID of the object that the object specified is in close proximity with. proximityID is a unique ID for the two objects in close proximity. 
   
 * **[stationary (Stream Processor)](#proximity)**

   This will return true when the object (defined in terms of longitude and latitude) becomes stationary within the specified radius. Returns false when the object moves out of the specified radius.
