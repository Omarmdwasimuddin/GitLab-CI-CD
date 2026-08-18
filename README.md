# GitLab CI/CD - Getting Started

## মূল কনসেপ্ট

CI/CD মানে হলো একটা continuous process — code লেখা, build করা, test করা, deploy করা, এবং monitor করা এই cycle টা বার বার চলতে থাকে।

এতে সুবিধা কী?
- নতুন code পুরনো buggy code এর উপর ভিত্তি করে develop হওয়ার chance কমে যায়
- Bug গুলো early stage-এই ধরা পড়ে
- Production-এ deploy হওয়া code established standards মেনে চলে তা নিশ্চিত হয়

---

## Step 1: Pipeline Configure করা

- Project-এর root-এ একটা `.gitlab-ci.yml` ফাইল বানাতে হয়
- ফাইলের নাম **case-sensitive**, তবে চাইলে custom filename-ও ব্যবহার করা যায়
- এই ফাইলে **stages** আর **jobs** define করা হয়:
  - **Stages** → কোন order-এ কাজ হবে (যেমন: `build` → `test` → `deploy`)
  - **Jobs** → প্রতিটা stage-এ আসলে কী কাজ হবে (যেমন: code compile করা বা test চালানো)

### Example

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  script:
    - echo "Compiling the code..."

test-job:
  stage: test
  script:
    - echo "Running tests..."
```

GitLab প্রতিবার pipeline trigger করে যখন:
- কোনো commit হয়
- Merge request হয়
- Schedule অনুযায়ী চালানো হয়
- Manually run করা হয়

এরপর একটা **runner** সেই pipeline-এর jobs গুলো execute করে।

---

## Step 2: Runner খুঁজে বের করা বা বানানো

Runner হলো সেই agent যেটা আসলে job গুলো run করে — physical machine বা virtual instance-এ।

- `.gitlab-ci.yml`-এ container image specify করা যায়
- Runner সেই image load করে → project clone করে → job run করে

**GitLab.com ব্যবহার করলে:**
- Linux, Windows, macOS runner already available

**Self-managed instance হলে:**
- Existing runner ব্যবহার করা যায়
- অথবা নিজের runner register করা যায় (local machine-এও করা সম্ভব)

---

## Step 3: CI/CD Variables ও Expressions

### CI/CD Variables

Key-value pair যেগুলো দিয়ে config settings বা sensitive info (password, API key) job-এ pass করা হয়।

Variable define করা যায়:
- `.gitlab-ci.yml` ফাইলে hard-code করে
- Project settings-এ
- Dynamically generate করে
- Project, group, বা instance level-এ

**Variable এর ধরন:**
- **Custom variables** — নিজে তৈরি ও manage করা হয় (UI/API/config ফাইলে)
- **Predefined variables** — GitLab automatically সেট করে দেয় (job/pipeline/environment সম্পর্কিত info)

**Security settings:**
- **Protected variables** — শুধু protected branch/tag-এ চলা job-এ accessible
- **Masked variables** — job log-এ value hide থাকে, যাতে sensitive info leak না হয়

### CI/CD Expressions

`$[[ ]]` syntax ব্যবহার করে, pipeline create করার সময় validate হয়।

- **Inputs context**: `$[[ inputs.INPUT_NAME ]]` → `include:inputs` দিয়ে বা নতুন pipeline run করার সময় pass করা typed parameter access করে
- **Matrix context**: `$[[ matrix.IDENTIFIER ]]` → matrix job dependencies-এ 1:1 mapping তৈরি করতে matrix values access করে

---

## Step 4: CI/CD Components

Component হলো একটা **reusable pipeline configuration unit** — পুরো pipeline অথবা তার ছোট অংশ হিসেবে ব্যবহার করা যায়।

- `include:component` দিয়ে pipeline-এ add করা হয়
- সুবিধা: code duplication কমে, maintainability বাড়ে, একাধিক project জুড়ে consistency থাকে
- নিজে component project বানিয়ে **CI/CD Catalog**-এ publish করে অন্য project-এও share করা যায়
- GitLab-এর নিজস্ব common task/integration-এর জন্য built-in component templates-ও আছে

---

## সংক্ষেপে Flow

```
.gitlab-ci.yml লেখা
        ↓
Runner সেটআপ করা
        ↓
Variables / Expressions দিয়ে dynamic config করা
        ↓
Components দিয়ে reuse করা
```
