# Ansible Collection - evrardjp.images_management - Ansible Cloud Images Management

Documentation for the collection.

## Reason of existence

Operating system images are in constant updating: Patches are applied to the packages part of the images on a frequent basis.
Your images could be outdated the day after they are built, as security patches can happen any time.

There are two ways to deal with outdated images (a cloud vendor should have both):
- On instance creation, ensure that all the packages of the image are updated
- Making sure that image is regularly rebuilt, and ensure that the user is only using the most recent/secure version by hiding the previous (outdated) versions.

If you have a cloud image management pipeline, you also:
- reduce the transit paid by the cloud vendor (if packages are not mirrored in the cloud vendor infrastructure)
- improve the speed of delivering a VM, which leads to better customer satisfaction (customers don't need to wait for large updates and reboots before starting to use their image)
- deliver images that can be "secure by default"
- have the opportunity to increase quality of the offering by adding your own tests
- have a common way to deal with different kind of images: container images, load balancer instances, openstack images, etc.

## Alternatives

Different tool exists:
-  https://github.com/osism/openstack-image-manager/ for managing openstack images. It just uploads images, no tests, no building
- Custom ansible playbooks
- Work done inside Zuul community for openstack infra, linked to nodepool.
- ...

## Requirements

Ansible 2.10
The right modules installed (openstack)

## Dependencies

None, outside ansible (and its dependencies).

## Example usage

TODO

## Contributing

All PRs welcome.

## Architecture

Everything is happening on the client side. It allows to run this tooling in CI, and observe the results into the usual developer/ops pipeline.

There are 3 building blocks:
- Image building.
  This block takes, as input, a series of user variables (including method to build), and use the appropriate role(s) to build a single image. 
  The output of this block is the generated image, which can be used for testing.
- Image testing/validation
  This block takes as input the generated image from the block 1 and a series of user variables (including method to test).
  The image is then tested in the appropriate roles, and returns success/failure for each tests of a single image.
- Image promotion/demotion
  This block ensures the new functioning image from block 1 is promoted for usage, and previous images are demoted.
  As this is very flexible and depends on your case, this project only delivers an EXAMPLE block for promotion/demotion. Please do what's necessary for your case.

## Code structure

- Image building block might require different building mechanism based on images (you don't build the same way a docker image and a qemu image).
  Each building mechanism is isolated in its own role.
- Image testing is similar to building: Each test mechanism is isolated in its own role.
- Image uploading is required for testing and for promotion. Uploads should be isolated and reusable.
