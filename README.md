# aws-ecr-docker-build-push
Build and push an image to ECR


## Example Usage

  build-backend:
    runs-on: ubuntu-latest
    outputs:
      image_uri: ${{ steps.build.outputs.image_uri }}
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure Git # Required because we are using submodules
        run: |
          git config --global user.email "my.email@gmail.com"
          git config --global user.name "myname"
          git config --global url.https://${{ secrets.PAT }}@github.com/.insteadOf https://github.com/

      - name: Checkout submodules
        run: git submodule update --init --recursive

      - name: Build Image
        id: build
        uses: ./.github/build-image
        with:
          repository_name: ${{ env.REPOSITORY_NAME }}
          image_prefix: "backend"
          build_directory: "backend"
          context_directory: "."
          dockerfile: "Dockerfile"
          filter_pattern: '["backend/**", "assets/**"]'
          build_env: '[{"name": "MODULE_ACCESS_MODE", "value": "DEFAULT"}]'
          aws_access_key_id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws_secret_access_key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          github_token: ${{ github.token }}
          pat: ${{ secrets.PAT }}