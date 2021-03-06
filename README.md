# Asyncio Geocoder

Load data from TIGER ADDRFEAT address ranges into Elasticsearch for fast geocoding
using address interpolation and Python 3's `asyncio`, as well as `docker-compose`
to make running the setup easier.

## Load ADDRFEAT Data

The Elasticsearch TIGER loading script reads directly from S3 into memory, but because
it uses `boto3`, you'll need valid AWS credentials set up through `aws-cli` or
exposed on your machine as environment variables. The operations we're running
won't have a cost for anyone reading the data, but `boto3` requires valid credentials.

Currently, there's an S3 bucket with this information ([viewer here](https://nvf-tiger-2016.s3.amazonaws.com/index.html)),
but you can create one with a similar setup by running the `create_tiger_s3.sh`
on any machine with `aws-cli` installed.

To run locally, just run:

```
docker-compose build
docker-compose up
```

### ES TIGER Data Loading

If you've successfully built and started the containers as shown above, you should
be able to start loading TIGER data with:

`docker-compose run geocoder es_tiger_loader.py WA`

Where `WA` is the two letter state abbreviation for the state you want to load
TIGER data from.

Alternatively, to restrict the search area more, you can enter the five digit
FIPS code for a state and county to load just that county. You can find all county
FIPS codes [at this Census link](https://www.census.gov/geo/reference/codes/cou.html).
For Cook County, IL:

`docker-compose run geocoder es_tiger_loader.py 17031`

### Running the Geocoder

To start the geocoder itself (which can run into issues if it's started at
the same time as the other containers), run:

**CSV:** `docker-compose run geocoder run.py -s WA ./data/input_file.csv`

**Postgres:** `docker-compose run geocoder run.py -s WA ./config.json `
