# Elastic Beanstalk Bug Reproduction

It is currently not possible to apply `Condition` statements to Resources in Cloudformation Templates. This is serious, because it severly limits the application area of elastic beanstalk and different resource packages for dev, staging and production environments.

## Reproduction

1. Clone this repository
2. Create a new Elastic Beanstalk application (`eb init`)
3. Create an environment (`eb create development --single`)
4. Modify `./.ebextensions/resources.config` by making the condition at the top false (set one of the "test" values to another value)

Expected file content of `./.ebextensions/resources.config` after step 4:
```yaml
Conditions:
  Test:
    Fn::Equals:
      - test
      - nottest

Resources:
  Bucket:
    Type: "AWS::S3::Bucket"
    Condition: Test

```
5. Add a commit to make sure eb picks up the changes (`git add --all && git commit -m 'test'`)
6. Run the deploy command (`eb deploy`)

-> You receive an error message:

```bash
ERROR: Service:AmazonCloudFormation, Message:Template format error: Unresolved resource dependencies [Bucket] in the Resources block of the template
ERROR: Failed to deploy application.

ERROR: ServiceError - Failed to deploy application.
```

To ensure that this is the issue, you can reset the Condition to `true` and `true`, add a git commit and `eb deploy` and then it will work again.
