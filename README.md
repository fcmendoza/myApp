## October 2025 updates

- Finally a version that works on iOS Safari! It's not the app itself, but at least the _Audtio Test_ page works. The url is: `https://d1xnsflhxa29nz.cloudfront.net/audio-test.html`. Load it on Safari on iOS and follow the instructions on the page.
- Removed tracking of `.vscode/extensions.json` from git.
- Updated CloudFront Distribution `E3HJNN1MOJFX3P` to cache for only 1 minute (60 seconds) instead of 24 hours (86400 seconds).
- Made audio-test.html dak mode friendly. To access it go to: `https://d1xnsflhxa29nz.cloudfront.net/audio-test.html`. Locally at: `http://localhost:8100/public/audio-test.html`

**How to develop:**

1. Make changes locally and test them using `ionic build && ionic serve`.
2. Once changes are good locally, deploy to AWS Amplify using `amplify publish --yes`.
3. After deployment, invalidate the CloudFront cache using: `aws cloudfront create-invalidation --distribution-id E3HJNN1MOJFX3P --paths "/*"`.

The URL I'm currently testing on my iOS devices is: https://d1xnsflhxa29nz.cloudfront.net which is linked to the `prod` environment in AWS Amplify.

For reference, for the `prod` environment:
* Environment: prod
* Hosting endpoint: https://d1xnsflhxa29nz.cloudfront.net
* CloudFront Distribution ID: E3HJNN1MOJFX3P

To update the CloudFront cache, I followed this process:

1. Get current config:

```sh
aws cloudfront get-distribution-config --id E3HJNN1MOJFX3P > full-config.json
```

2. Extract the inner DistributionConfig:

```sh
jq '.DistributionConfig' full-config.json > dist-config.json
```

3. Edit TTLs in dist-config.json:

```json
"DefaultCacheBehavior": {
    "MinTTL": 60,
    "DefaultTTL": 60,
    "MaxTTL": 360,
    ...
}
```

4. Update the distribution:

```sh
aws cloudfront update-distribution \
    --id E3HJNN1MOJFX3P \
    --distribution-config file://dist-config.json \
    --if-match E25KDLMQTL3Y0O
```

## August 2025 updates

We can make changes and test them locally using `ionic build && ionic serve`. Once we're good locally we can deploy the changes to the production environment by doing the following:

```sh
# First deploy to AWS Amplify
amplify publish --yes
# When the publish operation is complete, you'll get a URL where the app is up and running, but
# before loading that URL in the browser delete the cache by running:
aws cloudfront create-invalidation --distribution-id E3HJNN1MOJFX3P --paths "/*"
```

All in one:

```sh
amplify publish --yes \
&& aws cloudfront create-invalidation --distribution-id E3HJNN1MOJFX3P --paths "/*"
```

## July 2025 updates

```bash
amplify status  # will tell you  which environment we're using. It will also display the public URL where the app can be accessed.
amplify console # to open the Amplify Console and view your project status
amplify publish # will build all your local backend and frontend resources (if you have hosting category added) and provision it in the cloud
```

# README About

## To deploy the app

It seems I needed to run the following command for my changes to make it to AWS Amplify when deploying:

```bash
amplify pull --appId d3ztl4ai1m3z3 --envName dev
```

And for production:

```bash
#
# I got this command from the Amplify Console in AWS:
# https://us-east-1.console.aws.amazon.com/amplify/home?region=us-east-1#/d3ztl4ai1m3z3/YmFja2VuZA/prod
#
amplify pull --appId d3ztl4ai1m3z3 --envName prod
```

It's worth noting that this `pull` command does not deploy the application. For publishing the application follow the instructions under the "Deployment" section on this README.

## About the app

This is an Ionic app -- which at the moment I'm not sure what Ionic is, but that's what it is.

To build the app run the following command:

```bash
ionic build
```

To run the app locally use:

```bash
ionic serve
```

## Deployment

Changes don't need to be 'git commited' for them to be deployed.
To deploy the latest changes to AWS Amplify run the following:

```bash
npm run build && \
amplify publish
```

After the deployment is done you'll get a URL where the app is up and running. For example:

```
https://dwvszul5vv5qj.cloudfront.net
```

Sometimes (if not always) deployed changes to AWS Amplify do not reflect immediately. If you encounter this issue, try the following:

Run this AWS CLI command to invalidate the CloudFront cache:

```bash
aws cloudfront create-invalidation --distribution-id <your_distribution_id> --paths "/*"
```

e.g.: 

```bash
aws cloudfront create-invalidation --distribution-id E35ZKSFWVX20YL --paths "/*"
```

This process clears the cache for the CloudFront distribution, but it may take a few minutes to propagate. After running this command, refresh the CloudFront URL in your browser to see the latest changes.

Alternatively, you can manually invalidate the cache through the AWS CloudFront console:

1. Go to the AWS Cloundfront console.
2. Go to the _Distributions_ section.
3. Find the distribution for this app and then click on the "Invalidations" tab.
4. Create a new invalidation with the path `/*` to clear the cache. Or just accept the default values and click the "Create Invalidation" button.



<br/><br/><br/><br/>

## Project Overview: Meditation App

This is a meditation application built with the following technologies:

## Tech Stack:
- **Framework**: Ionic + Vue.js 3 (with Vue Router)
- **Build Tools**: Vite
- **Language**: TypeScript
- **Testing**: Cypress for E2E tests, Vitest for unit tests
- **Mobile Development**: Capacitor for cross-platform mobile capabilities
- **Deployment**: AWS Amplify with S3 and CloudFront for hosting

## Application Features:
1. **Meditation Timer**:
   - Adjustable meditation duration (0-20 minutes)
   - Bell interval settings (15 seconds to 4 minutes)
   - Different bell sound options (Heart Chakra Bell, Copper Bell Ding)
   
2. **Background Music**:
   - Multiple meditation music options (Therapy, Deep, Peaceful)
   - Audio controls for playing/stopping meditation sessions

3. **Navigation**:
   - Tab-based navigation system
   - Main focus on the Meditation page, which is the default landing page
   - Additional tabs (Tab1, Tab2, Tab3) for other functionality

## Deployment:
- The app is configured for deployment via AWS Amplify
- It has S3 and CloudFront set up for web hosting
- The CloudFront URL is: https://dwvszul5vv5qj.cloudfront.net

## Project Structure:
- Standard Ionic/Vue structure with views, components, and routing
- Audio assets stored in the public/assets/sounds directory
- AWS Amplify configuration for cloud deployment

This appears to be a meditation application that provides users with customizable meditation sessions including timer controls, interval bells, and background music options. The app is designed to work across platforms using Ionic and Capacitor, and it's set up for deployment on AWS infrastructure.
