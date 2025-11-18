name: Run Kaggle Notebook
on:
  push:
    branches:
      - main
  schedule:
    - cron: '0 */12 * * *'  # Every 12 hours (00:00 and 12:00 UTC)
  workflow_dispatch:
jobs:
  run-notebook:
    runs-on: ubuntu-latest
    
    steps:
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'
      
      - name: Install Kaggle CLI
        run: pip install --quiet kaggle
      
      - name: Configure Kaggle Credentials
        run: |
          mkdir -p ~/.kaggle
          echo '{"username":"shreevathsbbhh","key":"9f167cdee8a045c97ca6a2f82c6701f9"}' > ~/.kaggle/kaggle.json
          chmod 600 ~/.kaggle/kaggle.json
      
      - name: Pull Notebook and Metadata from Kaggle
        run: |
          echo "📥 Pulling notebook with metadata..."
          kaggle kernels pull shreevathsbbhh/new-15 -p ./notebook -m
          if [ ! -f "./notebook/new-15.ipynb" ]; then
            echo "❌ Failed to download"
            exit 1
          fi
          if [ -f "./notebook/kernel-metadata.json" ]; then
            echo "✅ Metadata downloaded (datasets preserved)"
          else
            echo "⚠️ No metadata file"
          fi
      
      - name: Push Notebook to Kaggle
        run: |
          cd ./notebook
          echo "📤 Pushing to Kaggle..."
          kaggle kernels push
          if [ $? -eq 0 ]; then
            echo "✅ Push successful"
          else
            echo "❌ Push failed"
            exit 1
          fi
      
      - name: Verify Execution
        run: |
          sleep 20
          kaggle kernels status shreevathsbbhh/new-15 || true
          echo "✅ Complete"
          echo "🔗 https://www.kaggle.com/code/shreevathsbbhh/new-15"
      
      - name: Summary
        if: always()
        run: |
          echo "## Execution Summary" >> $GITHUB_STEP_SUMMARY
          echo "- **Time:** $(date -u)" >> $GITHUB_STEP_SUMMARY
          echo "- **Trigger:** ${{ github.event_name }}" >> $GITHUB_STEP_SUMMARY
          echo "- **Status:** ${{ job.status }}" >> $GITHUB_STEP_SUMMARY
          echo "- **Next Run:** 12 hours" >> $GITHUB_STEP_SUMMARY
