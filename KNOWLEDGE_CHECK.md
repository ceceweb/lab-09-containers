# Lab 09: Knowledge check

**Answer each question below it, in your own words, in one to three sentences.** Replace the `...` below **Your answer:** with your text.

## 1. Credentials in the image?

Assume you want to share your image with others and make it publicly available on `ghcr.io`, so that anyone can pull it and run the analysis. A teammate suggests adding `COPY .aws /home/app/.aws` to the `Dockerfile`, so that nobody has to type `-v ~/.aws:/home/app/.aws:ro` any more. (This is a thought experiment: keep your own package private.)

**Your answer:** Give two reasons why this is a bad idea. Think of who could then read your credentials, and of what happens when your Learner Lab session ends.

This is a bad idea because anyone who pulls a public image would get every file in it including my AWS credentials and now would have access to my S3 bucket and the SSM parameter that contains my Atlas password. Also, the Learner lab credentials expire after a few hours, so someone would have to rebuild the image every few hours to make the image work again.

## 2. Where did the Atlas password come from?

In Step 4, you ran the analysis once with `-v ~/.aws:/home/app/.aws:ro` only, without the three `-e` options. The container still wrote to your Atlas cluster.

*Hint:* Read `get_atlas_settings()` in `src/book_analysis/analyze.py`.

**Your answer:** Explain, step by step, where the container found the Atlas settings in that run.

The container had no -e options so MONGODB_ATLAS_URL wasn't set so then the -v mount gave the container my AWS credentials. With those, boto3 read the SSM parameter mongodb_credentials, got the Atlas url, user, and password and was able to connect to Atlas

## 3. Same tag, different image

Run `docker images book-analysis`, and note the image ID of `book-analysis:0.1.0`. Then change the `description` in the `LABEL` of your `Dockerfile`, build again with the same command, `docker build -t book-analysis:0.1.0 .`, and run `docker images book-analysis` again.

**Your answer:** Paste the image ID from before and after the rebuild. Why is it a problem for someone who uses your image that `0.1.0` now names a different image, and what should you have done instead?

eab2a4954c11 (before) and 20688ecb7754 (after)
0.1.0 naming a different image is now a problem because the two images should have different names, so when a different person pulls or runs 0.1.0, they won't be able to tell if they got a different image, so if their results change, they can't reproduce last week's run or tell what changed. The fix I would make is to name the next version 0.1.1