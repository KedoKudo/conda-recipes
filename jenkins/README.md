# Jenkins configuration and scripts

There several Jenkins projects in builds.mantidproject.org to build the conda package nightly.

## master_clean-rhel7
Among other things, it generates an artifact called "conda_update_recipe.py" with the right version number of mantid.

## master_condarecipes_update
* Update mantid-framework conda recipe by running "conda_update_recipe.py".
* git push to mantidproject/conda-recipes

## master_create_conda_linux_pkg
Run conda build by using docker. This is done only at ornl-manos. 
The conda package will be built as an artifact.

  `cd docker/framework && ./run_docker_build.sh`

## master_systemtests-conda
* Run system tests by using the conda package built in the last step
* If all tests pass, upload conda package to anaconda

```
rm -rf conda-recipes
git clone https://github.com/mantidproject/conda-recipes
rsync -av conda-recipes/jenkins/Testing/ Testing/ # update Testing to run tests using conda package
./buildconfig/Jenkins/systemtests && \
	ANACONDA_PASSWORD=$ANACONDA_PASSWORD conda-recipes/jenkins/upload.sh -l nightly --force $(ls build/mantid-framework-*.tar.bz2)
```