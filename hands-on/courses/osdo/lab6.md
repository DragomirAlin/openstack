# Cinder

`cinder-volume` is there to create the actual storage volumes.
`cinder-scheduler` decides where cinder-volume will create a requested block device (if there is more than one location to choose from).
`cinder-api`, like all other OpenStack services, implements a ReSTful API into Cinder.
