# sagemaker-forecast-flight-delays

Use Amazon SageMaker to forecast US flight delays using SageMaker's built-in linear learner algorithm to craete a regression model.

![Final Environment](https://user-images.githubusercontent.com/3911650/56763994-ea7b9600-6760-11e9-9c8a-59fd2f90d2c9.png)

## Getting Started

1. Create IAM resources and CloudFormation stack for lab student:

    ```sh
    group="students"
    user="student"
    password="password"

    aws iam create-group --group-name $group
    aws iam put-group-policy --group-name $group --policy-name "lab" --policy-document file://./infrastructure/policy.json
    aws iam create-user --user-name $user
    aws iam create-login-profile --user-name $user --password $password
    aws iam add-user-to-group --group-name $group --user-name $user

    aws cloudformation create-stack --stack-name "cloudacademylabs" --template-body file://.//infrastructure/cloudformation.yaml --capabilities "CAPABILITY_IAM" --on-failure "DO_NOTHING"
    do {
        Start-Sleep 5
        $response=aws cloudformation describe-stacks --stack-name "cloudacademylabs"
    } while ($response.Contains("CREATE_IN_PROGRESS"))
    $response
    aws cloudformation describe-stack-resources --stack-name "cloudacademylabs"
    ```

## Tearing Down

1. Delete any resources created during the lab.

2. Delete CloudFormation stack and IAM resources for lab student:

    ```sh
    aws cloudformation delete-stack --stack-name "cloudacademylabs"

    aws iam remove-user-from-group --group-name $group --user-name $user
    aws iam delete-login-profile --user-name $user
    aws iam delete-user --user-name $user
    aws iam delete-group-policy --group-name $group --policy-name "lab"
    aws iam delete-group --group-name $group
    ```

## Instructions

1. Create a SageMaker training job using the linear learner algorithm. Set:
    - Role ARN to the ARN of the `sagemaker-role` created by the CloudFormation template
    - `model_type` to `regressor` and `feature_dim` to 716
    - Configure the `train` data channel to point to the `Flight_training.csv` file in S3
    - Configure a second `test` data channel to point to the `Flight_test.csv` file in S3
1. Create a model using the model output artifacts created in S3 by the training job
1. Create an endpoint to serve the model
1. In Cloud9, issue the following command to retrieve an inference from the endpoint and save it in a file named `prediction.json`:

    ```sh
    # test flight between San Francisco and Indianapolis
    flight_vector=1550,1609,19.0,19.0,1,1,2307,257.0,1943.0,1,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0
    aws sagemaker-runtime invoke-endpoint --endpoint-name flight-delays --content-type 'text/csv' --body $flight_vector prediction.json
    ```

## Cleaning Up

Delete Endpoint, endpoint configuration, model, and S3 bucket data. Then delete the CloudFormation stack to remove all the remaining resources used in the Lab.

## Acknowledgements

Thanks to the US Department of Transportation Bureau of Transportation Statistics for providing the Airline On-Time Performance data [here](https://www.transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20OnTime%20Performance%20Data&DB_Short_Name=On-Time). For more on the data, including the raw data itself visit [my repository performing a similar task using Amazon Machine Learning](https://github.com/lrakai/aws-ml-regression/tree/master/data).