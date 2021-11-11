# Chaotic Good Review Service

This is a simple service that's going to be used in the Chaotic Good project. It will probably be monitored with prometheus and grafana and run in fargate. It will be completely stateless.

## Endpoints

This service responds to requests for reviews and queries a backend datastore to get them. It has a few different endpoints for the following functions:

- Get one review

/review/:review-id

GET request, review ID in the query param

- Get multiple reviews

/reviews

GET request, with array of review IDs in the request body


- Create a review

/review

POST request, with the review data in the request body as follows:

```
reviewAuthor: string,
reviewUserName: string,
reviewAuthorEmail: string,
reviewRating: int (1-5),
reviewSubmittedDate: Date,
reviewText: string,
```

- Search reviews

/search

GET request, with search terms in the request body

We want to be able to search for reviews submitted between two times, as well as reviews containing a number of keywords

## Architecture

This will be written in NodeJS and run in fargate. It will have an ALB in front of it, and a minimum of 3 tasks in the service. Scaling will probably be determined by CPU, though we'll see what it takes to kill the DB behind it.

It's going to use express, since that's quick and easy to set up, and we don't need anything fancy here.

We'll also be using security groups to restrict the origin to anything inside the VPC or the specific cloudfront distribution.

## Deployment

I don't know where/how we'll build it yet, but something like a circle CI free tier account would be a good option. Alternately, we might do it in GitHub actions. It's highly likely that we won't necessarily care about the deployment in this repo, and instead just build the container image and push it to ECR so that the corresponding terraform side of things can actually set up the service in fargate.

This means that the only thing we need in terms of credentials associated with this repo is for an AWS service role that only pushes to ECR (it might need a few more ECR IAM permissions like CompleteLayerUpload, but we can add those as needed).
