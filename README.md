# Run

This is a really rough readme.

What --mode=train now does is that it trains a given model (where the model name is currently defined in the config file). The training is the same as before, and the model parameters (officially called the state dictionary) is now saved together. Can be ran via:

`python baler --config=projects/cms/configs/cms.json --input=projects/cms/data/cms_data.root --output=projects/cms/output/ --mode=train`

which will, most importantly, output: current_model.pt . This contains all necessary model parameters.

To do something with this model, you can now choose --mode=compress, which can be ran as

`python baler --config=projects/cms/configs/cms.json --input=projects/cms/data/cms_data.root --output=projects/cms/output/ --model=projects/cms/output/current_model.pt --mode=compress`

This will output a compressed file called compressed.pickle, and this is the latent space representation of the input dataset. It will also output cleandata_pre_comp.pickle which is just the exact data being compressed.

To decompress the compressed file, we choose --mode=decompress and run:

`python baler --config=projects/cms/configs/cms.json --input=projects/cms/output/compressed.pickle --output=projects/cms/output/ --model=projects/cms/output/current_model.pt --mode=decompress`

which outputs decompressed.pickle . To double check the file sizes, we can run

`python baler --config=projects/cms/configs/cms.json --input=projects/cms/output/ --output=projects/cms/output/ --mode=info`

which will print the file sizes of the data we’re compressing, the compressed dataset & the decompressed dataset.

Plotting works as before, with a minor caveat. This caveat is that the column names are currently manually implemented because I couldn’t find a simple way to store the column names (there is a good explanation for this), so it will not run immediately on the UN dataset without modifications to the config file. Plotting would look something like this however:

`python baler --config=projects/cms/configs/cms.json --input=projects/cms/output/cleandata_pre_comp.pickle --output=projects/cms/output/decompressed.pickle --mode=plot`

# Running with Docker #

Running with Docker requires slight modifications to the above commands. The base command becomes:

```console
foo@bar $ docker run --mount type=bind,source=${PWD}/projects/,target=/projects ghcr.io/uomresearchit/baler:latest 
```

Where:
  * `docker run` invokes docker and specifies the running of a container
  * `--mount type=bind,source=${PWD}/projects/,target=/projects` mounts the local (host) directory `./projects` to the container at `/projects`
  * `ghcr.io/uomresearchit/baler:latest` specifies the container to run
  
Therefore the three commands detailed above become:

### Train: ###

```console
foo@bar $ docker run --mount type=bind,source=${PWD}/projects/,target=/projects ghcr.io/uomresearchit/baler:latest --config=/projects/cms/configs/cms.json --input=/projects/cms/data/cms_data.root --output=/projects/cms/output/ --mode=train
```

### Compress: ### 
```console
foo@bar $ docker run --mount type=bind,source=${PWD}/projects/,target=/projects ghcr.io/uomresearchit/baler:latest --config=/projects/cms/configs/cms.json --input=/projects/cms/data/cms_data.root --output=/projects/cms/output/ --model=/projects/cms/output/current_model.pt --mode=compress
```

### Decompress: ###
```console
foo@bar $ docker run --mount type=bind,source=${PWD}/projects/,target=/projects ghcr.io/uomresearchit/baler:latest --config=/projects/cms/configs/cms.json --input=/projects/cms/output/compressed.pickle --output=/projects/cms/output/ --model=/projects/cms/output/current_model.pt --mode=decompress
```

## Build Docker image: ##

If you would prefer not to use the Docker image produced by the University of Manchester, you may build the image ourself. This is achived with:

```console
foo@bar $ docker build -t baler:latest .
```

This image may be run using (e.g.):

```console
foo@bar $ docker run --mount type=bind,source=${PWD}/projects/,target=/projects baler:latest --config=/projects/cms/configs/cms.json --input=/projects/cms/data/cms_data.root --output=/projects/cms/output/ --mode=train
```
