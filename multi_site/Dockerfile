# FROM nginx:alpine

# COPY sites/* /usr/share/nginx/html/
# COPY conf.d/* /etc/nginx/conf.d/

# # Install Trivy for vulnerability scanning
# RUN apk add --no-cache curl \
#     && curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin \
#     && trivy filesystem --exit-code 1 --no-progress /

# # Expose the port
# EXPOSE 80

# Stage 1: Build the application image
FROM nginx:alpine as builder

COPY sites/* /usr/share/nginx/html/
COPY conf.d/* /etc/nginx/conf.d/

# Stage 2: Scan the application image using Trivy
FROM aquasec/trivy:latest as trivy

# Copy the built image from the builder stage
COPY --from=builder / /

#docker image build context

# Stage 3: Final lightweight image
FROM nginx:alpine

# Copy files from the builder stage
COPY --from=builder / /

# Copy files from the trivy stage
COPY --from=trivy /usr/local/bin/trivy /usr/local/bin/trivy

# Run Trivy scan on the copied image and save the results to a file
RUN trivy image nginx:alpine --format json > /usr/share/nginx/html/trivy_report.json && \
    echo "Trivy report saved to /trivy_report.json"

# Expose port if necessary
EXPOSE 80

# Start Nginx or your application
CMD ["nginx", "-g", "daemon off;"]
