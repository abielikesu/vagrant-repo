#!/bin/bash
#set -x

# Absolute path to this script
SCRIPT=$(readlink -f $0)
# Absolute path this script is in
SCRIPTPATH=`dirname $SCRIPT`

TEMPLATE_DIR=${SCRIPTPATH}/template

REPO_URL=/u01/packer/boxrepo
TEMPLATE1=${TEMPLATE_DIR}/vbox.template1.json
TEMPLATE2=${TEMPLATE_DIR}/vbox.template2.json
BOX_CHECKSUM_TYPE="sha1"

P_BOX_NAME=$1
P_BOX_VERSION=$2
P_BOX_FILE=$3
P_BOX_DESCRIPTION=$4

BOX_FILE=$(basename "${P_BOX_FILE}")
BOX_FILE_EXT="${BOX_FILE##*.}"
BOX_FILE_NAME="${BOX_FILE%.*}"

BOX_REMOTE_NAME=${BOX_FILE_NAME}.${P_BOX_VERSION}.${BOX_FILE_EXT}
BOX_REMOTE_PATH=${REPO_URL}/${P_BOX_NAME}/boxes
BOX_REMOTE=${BOX_REMOTE_PATH}/${BOX_REMOTE_NAME}

BOX_JSON_FILE=${P_BOX_NAME}.json
BOX_JSON_PATH=${REPO_URL}/${P_BOX_NAME}
BOX_JSON=${BOX_JSON_PATH}/${BOX_JSON_FILE}

#Check if the source box file exists
if [ ! -f ${P_BOX_FILE} ]; 
then
  echo "Box file [ ${P_BOX_FILE} ] doesn't exist. Aborting."
  exit 1
else
  # Calculation checksum
  echo "Calculating the cheksum for [ ${P_BOX_FILE} ]."
  BOX_CHECKSUM=$(openssl sha1 $P_BOX_FILE | awk '{print $2}')
fi

#Create the box dir in the repository
mkdir -p ${REPO_URL}/${P_BOX_NAME}/boxes

echo "Validating box json file  [ ${BOX_JSON} ]. "
#Check if the box json file exists
if [ ! -f ${BOX_JSON} ]; 
then
  echo "Creating new box json file"
  touch ${BOX_JSON} 
  COMA="    "
  # Create the box template
  TEMP1=$(mktemp /tmp/template1.XXXXXX)
  sed -e "s!BOX_NAME!$P_BOX_NAME!g"               \
      -e "s!BOX_DESCRIPTION!$P_BOX_DESCRIPTION!g" \
      < $TEMPLATE1 > $TEMP1
  #cat $TEMP1
  cat $TEMP1 >  ${BOX_JSON}

  rm $TEMP1
else
  COMA="   ,"
fi

# Check if the version is already registered
VE=$(sed -n "/\"version\": \"$P_BOX_VERSION\"/=" ${BOX_JSON})

if [ -z "$VE" ]; 
then
  # Create the version template
  TEMP2=$(mktemp /tmp/template2.XXXXXX)
  sed -e "s#BOX_VERSION#$P_BOX_VERSION#g"            \
      -e "s#BOX_FILE_NAME#$BOX_REMOTE#g"             \
      -e "s#BOX_CHECKSUM_TYPE#$BOX_CHECKSUM_TYPE#g"  \
      -e "s#BOX_CHECKSUM#$BOX_CHECKSUM#g"            \
      -e "s#COMA#$COMA#g"  < $TEMPLATE2 > $TEMP2
  
  #cat $TEMP2
  
  # Find the location to add a new version
  LINEN=$(( $(sed -n '/]$/ {N; /\n}$/=}' ${BOX_JSON}) - 2))
  
  # Add the temporary template, afgter the line number
  sed -i "$LINEN r $TEMP2" ${BOX_JSON}

  rm $TEMP2
else
  echo "Version ${P_BOX_VERSION} already registered."
fi


#cat ${BOX_JSON}

# Copy the box file to the repository
# New name: <boxfile>.<version>.<boxextention>
echo "Uploading box file [ ${BOX_REMOTE} ] to the repository"
cp -v ${P_BOX_FILE} ${BOX_REMOTE}

