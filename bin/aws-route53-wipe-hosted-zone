#!/bin/bash
#
# DANGEROUS!
#
# aws-route53-wipe-hosted-zone - Delete a Route 53 hosted zone with all contents
#
set -e
VERBOSE=true

for domain_to_delete in "$@"; do
  $VERBOSE && echo "DESTROYING: $domain_to_delete in Route 53"

  hosted_zone_id=$(
    aws route53 list-hosted-zones \
      --output text \
      --query 'HostedZones[?Name==`'$domain_to_delete'.`].Id'
  )
  $VERBOSE &&
    echo hosted_zone_id=${hosted_zone_id:?Unable to find: $domain_to_delete}

  aws route53 list-resource-record-sets \
    --hosted-zone-id $hosted_zone_id |
  jq -c '.ResourceRecordSets[]' |
  while read -r resourcerecordset; do
    read -r name type <<<$(jq -r '.Name,.Type' <<<"$resourcerecordset")
    if [ $type == "NS" -o $type == "SOA" ]; then
      $VERBOSE && echo "SKIPPING: $type $name"
    else
      change_id=$(aws route53 change-resource-record-sets \
        --hosted-zone-id $hosted_zone_id \
        --change-batch '{"Changes":[{"Action":"DELETE","ResourceRecordSet":
            '"$resourcerecordset"'
          }]}' \
        --output text \
        --query 'ChangeInfo.Id')
      $VERBOSE && echo "DELETING: $type $name $change_id"
    fi
  done

  change_id=$(aws route53 delete-hosted-zone \
    --id $hosted_zone_id \
    --output text \
    --query 'ChangeInfo.Id')
  $VERBOSE && echo "DELETING: hosted zone for $domain_to_delete $change_id"
done
