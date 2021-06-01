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
