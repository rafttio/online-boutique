# Copyright 2021 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# https://mcr.microsoft.com/v2/dotnet/sdk/tags/list
FROM --platform=$BUILDPLATFORM mcr.microsoft.com/dotnet/sdk:7.0.402 as builder

ARG TARGETARCH

WORKDIR /app
COPY cartservice.csproj .
RUN dotnet restore cartservice.csproj -a $TARGETARCH
COPY . .
RUN dotnet publish cartservice.csproj -a $TARGETARCH -c Release -o /cartservice --no-restore

FROM mcr.microsoft.com/dotnet/aspnet:7.0.12@sha256:77ea66eb200fa06d2929cc937b75724bb2f5cdf2bcf6dc6aaf0851bb75bb631f

RUN apt-get update \
    && apt-get install -y unzip procps wget

ARG TARGETARCH
# renovate: datasource=github-releases depName=grpc-ecosystem/grpc-health-probe
ENV GRPC_HEALTH_PROBE_VERSION=v0.4.15
RUN wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-${TARGETARCH} && \
    chmod +x /bin/grpc_health_probe

COPY --from=builder /cartservice .
EXPOSE 7070
ENV DOTNET_EnableDiagnostics=0 \
    ASPNETCORE_URLS=http://*:7070
USER 1000

ENTRYPOINT ["dotnet", "cartservice.dll"]
