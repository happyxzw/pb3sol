INPUT_DIR=/in
OUTPUT_DIR=/out
CONTAINER=umegaya/pb3sol
CONTAINER_RUNARG=docker run --rm -ti -v `pwd`/contracts/libs/pb:${OUTPUT_DIR} -v `pwd`/proto:${INPUT_DIR} -v `pwd`/../src/protoc:/protoc ${CONTAINER}
SOLIDITY_PLUGIN=/protoc/plugin/gen_sol.py

define soliditize
${CONTAINER_RUNARG} protoc -I/ -I/protoc/include --plugin=protoc-gen-sol=${SOLIDITY_PLUGIN} --sol_out=$2 $1
endef

.PHONY: proto
proto:
	$(call soliditize,${INPUT_DIR}/TaskList.proto,"gen_runtime=True:${OUTPUT_DIR}")

test_proto: proto
	sed -e 's/.solidity.int128 f4 = 5;/.solidity.int128 f4 = 5;.solidity.uint256 new_id = 6;/g' proto/TaskList.proto > proto/TaskList2.proto
	$(call soliditize,${INPUT_DIR}/TaskList2.proto,"pb_libname=pb2:${OUTPUT_DIR}")

shell:
	docker run --rm -ti -v `pwd`/contracts/libs/pb:${OUTPUT_DIR} -v `pwd`/proto:${INPUT_DIR} -v `pwd`/../src/protoc:/protoc ${CONTAINER} bash

run: test_proto
	bash ./tools/run_chain.sh
	truffle test
	kill `cat ./test_chain.pid`

image:
	cd tools/container && docker build -t umegaya/pb3sol_test .