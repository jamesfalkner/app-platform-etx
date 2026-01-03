# Lab Module Generator

Guide you through creating a single Red Hat Showroom workshop module from reference materials (URLs, files, docs, or text) with business storytelling and proper AsciiDoc formatting.

## When to Use

**Use this skill when you want to**:
- Create a new workshop module from scratch
- Convert documentation into hands-on lab format
- Add a module to an existing workshop
- Transform technical content into engaging learning experience

**Don't use this for**:
- Creating demo content → use `/demo-module`
- Converting to blog posts → use `/blog-generate`
- Reviewing existing content → use workshop-reviewer agent

## Workflow

### Step 1: Gather Requirements

I'll ask you these key questions:

1. **Module file name**:
   - Module file name (e.g., "03-module-01.adoc", "04-pipelines-setup.adoc")
   - Files go directly in `content/modules/ROOT/pages/`
   - Pattern: `[number]-[topic-name].adoc`

2. **AgnosticV catalog item** (optional but recommended):
   - Is this based on an AgnosticV catalog item?
   - If yes: Provide catalog item name (e.g., "ocp4_workload_rhods_demo")
   - Default AgnosticV path: `~/work/code/agnosticv/`
   - I'll read the catalog item to extract UserInfo variables

3. **Reference materials**:
   - URLs to Red Hat docs
   - Local file paths (Markdown, AsciiDoc, text, PDF)
   - Or paste content directly

4. **Main learning objective**:
   - Example: "Create and run a CI/CD pipeline with Tekton"

5. **Business scenario**:
   - Auto-detect from previous module (if exists)
   - Or ask for company name (default: ACME Corp)

6. **Technology/product focus**:
   - Example: "OpenShift Pipelines", "Podman"

7. **Number of exercises**:
   - Recommended: 2-3

### Step 2: Extract AgnosticV UserInfo Variables (if applicable)

If you provided an AgnosticV catalog item, I'll:

**Read AgnosticV catalog configuration**:
- Location: `~/work/code/agnosticv/`
- Find catalog item directory: `catalogs/<item-name>/`
- Read `common.yaml` for workload list and variables

**Identify workload roles from AgnosticD**:
- AgnosticD v2: `~/work/code/agnosticd-v2/`
- AgnosticD v1: `~/work/code/agnosticd/`
- Read workload roles referenced in common.yaml

**Extract UserInfo variables**:
- Search for `agnosticd_user_info` tasks in workload roles
- Extract variables from `data:` sections
- Common variables:
  - `bastion_public_hostname`
  - `openshift_console_url`
  - `openshift_api_url`
  - `user_name`, `user_password`
  - Custom workload-specific variables

**Map to Showroom attributes**:
```asciidoc
{bastion_public_hostname}
{openshift_console_url}
{user}
{password}
{{ custom_variable }}
```

**Example extraction**:
```yaml
# From workload role tasks/main.yml
- name: Save cluster info
  agnosticd.core.agnosticd_user_info:
    data:
      openshift_console_url: "{{ r_openshift_console_url }}"
      openshift_api_url: "{{ r_openshift_api_url }}"
      bastion_public_hostname: "{{ hostvars['bastion']['public_dns_name'] }}"
```

**Result**: I'll use these as Showroom variables in the generated module.

### Step 3: Fetch and Analyze References

Based on your references, I'll:
- Fetch URLs with WebFetch
- Read local files (supports PDF)
- Extract procedures, commands, concepts
- Identify hands-on opportunities
- Combine with AgnosticV variables (if provided)

### Step 4: Read Templates

I'll always read these before generating:
- `content/modules/ROOT/pages/workshop/templates/03-module-01.adoc`
- `content/modules/ROOT/pages/workshop/example/03-module-01.adoc`
- `.claude/prompts/redhat_style_guide_validation.txt`

### Step 5: Generate Module

I'll create a complete module with:

**Structure**:
- Learning objectives (3-4 items)
- Business introduction with scenario
- 2-3 progressive exercises
- Step-by-step instructions with commands
- Verification steps
- Image placeholders
- Module summary

**Quality**:
- Valid AsciiDoc syntax
- Proper Red Hat product names
- Sentence case headlines
- Second-person narrative
- Code blocks with syntax highlighting

### Step 6: Validate

I'll automatically run:
- **workshop-reviewer** agent: Validates structure and pedagogy
- **style-enforcer** agent: Applies Red Hat style standards

### Step 7: Update Navigation

I'll ask if you want me to add the module to `content/modules/ROOT/nav.adoc` (required for Showroom sidebar).

### Step 8: Deliver

You'll get:
- Complete module file at `content/modules/ROOT/pages/<module-file>.adoc`
- Screenshot capture guide
- Navigation xref snippet
- Review checklist
- Next module suggestions

## Example Usage

### Example 1: Create Module from Red Hat Docs

```
User: Create Module 1 for OpenShift Pipelines using:
      https://docs.openshift.com/pipelines/1.12/

Skill: I'll create that module. Let me ask a few questions...
       [asks 7 questions]
       [generates module with ACME Corp scenario]
       [validates with agents]

✓ Created: content/modules/ROOT/pages/03-pipelines-intro.adoc
✓ 3 exercises: Create task, Define pipeline, Run pipeline
✓ Screenshot guide provided
✓ Ready to add to nav.adoc
```

### Example 2: Create Module with AgnosticV Integration

```
User: Create Module 1 for Red Hat OpenShift AI demo

Skill: Let me gather information...

Q1: Module file name? → "03-rhods-intro.adoc"
Q2: AgnosticV catalog item? → "ocp4_workload_rhods_demo"
Q3: Reference materials? → https://docs.redhat.com/en/documentation/red_hat_openshift_ai/
Q4: Learning objective? → "Deploy and test AI/ML workloads on OpenShift AI"
Q5: Business scenario? → ACME Corp (first module)
Q6: Technology focus? → "Red Hat OpenShift AI"
Q7: Exercise count? → 3

[Reads AgnosticV catalog]
→ Found: ~/work/code/agnosticv/catalogs/ocp4_workload_rhods_demo/common.yaml
→ Workloads: rhpds.rhods_demo.ocp4_workload_rhods_demo

[Reads AgnosticD workload]
→ Location: ~/work/code/agnosticd-v2/collections/ansible_collections/rhpds/rhods_demo/
→ Extracted UserInfo variables:
  - openshift_console_url
  - rhods_dashboard_url
  - user_name
  - user_password

[Generates module with:]
- Variables mapped to Showroom attributes:
  * {openshift_console_url}
  * {rhods_dashboard_url}
  * {user}
  * {password}
- Commands using these variables
- Example: "Log into OpenShift Console at {openshift_console_url}"

✓ Created: content/modules/ROOT/pages/03-rhods-intro.adoc
✓ AgnosticV variables integrated
✓ 3 exercises with dynamic variables
✓ Screenshot guide provided
```

## Tips for Best Results

- **Specific objectives**: "Build and deploy container with persistent storage" vs "Learn containers"
- **Multiple references**: More context = better content
- **Continue scenarios**: Reference previous module for narrative continuity
- **Test commands**: Always verify in real environment

## Quality Standards

Every module will have:
- ✓ Valid AsciiDoc syntax
- ✓ 3-4 clear learning objectives
- ✓ Business context introduction
- ✓ Progressive exercises (foundational → advanced)
- ✓ Verification steps
- ✓ Module summary
- ✓ Image placeholders
- ✓ Red Hat style compliance
