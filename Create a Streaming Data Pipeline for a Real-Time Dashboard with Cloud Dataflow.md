Create a Streaming Data Pipeline for a Real-Time Dashboard with Cloud Dataflow

bq mk taxirides


bq mk \
--time_partitioning_field timestamp \
--schema ride_id:string,point_idx:integer,latitude:float,longitude:float,\
timestamp:timestamp,meter_reading:float,meter_increment:float,ride_status:string,\
passenger_count:integer -t taxirides.realtime



gcloud dataflow jobs run streaming-taxi-pipeline --gcs-location gs://dataflow-templates-europe-west2/latest/PubSub_to_BigQuery --region europe-west2 --staging-location gs://qwiklabs-gcp-00-d7154ebfdd46/tmp/ --parameters inputTopic=projects/pubsub-public-data/topics/taxirides-realtime,outputTableSpec=qwiklabs-gcp-00-d7154ebfdd46:taxirides.realtime

    