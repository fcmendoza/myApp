# Human Generated README

NOTE: it seems I needed to run the following command for my changes to make it to AWS Amplify when deploying:

```bash
amplify pull --appId d3ztl4ai1m3z3 --envName dev
```

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

We can deploy this app to AWS Amplify. To deploy the latest changes run:

```bash
npm run build && \
amplify publish
```

The public URL where this app runs is:

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